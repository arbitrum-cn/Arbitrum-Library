# Inbox
Inbox是用于存放l2 da（数据可用性）的合约，该合约保证了l2上的数据可用性，数据可用性是确保l2安全最关键的一环，其原因在于该da包含了l2上交易基本的信息，见[sequencerMessage](./sequencerMessage.md),并且这些交易也按照顺序进入了inbox,因此后续任何人可以通过读取l1中的inbox获得历史交易摘要，并将这些交易重新执行，便可获得l2上最新的状态，因此任何人都可以验证l2上的状态，并且只需要有一人诚实，用户便可获得正确的状态（或者用户自行运行node来验证）。

这一切使得l2中的安全保障能控制为1/n（即网络中有n个节点，我们只需要1个诚实，结果便可正确），而这一切最关键的地方则是l1上的inbox合约，下面，我们将解析inbox合约处理消息的过程：

## Sequencer Inbox
Sequencer Inbox中包含了l2交易的输入信息（也就是da信息），点击[此处](https://github.com/OffchainLabs/nitro/blob/master/contracts/src/bridge/SequencerInbox.sol)访问代码。
在inbox里，我们可以看到有以下变量，其中包含了inbox中消息的摘要汇总，bridge合约地址, batch规范信息，时间间隔信息（如delayed inbox中的交易最早可以多久被并入inbox中），以及数据可用下委员会的密钥信息（主要用于anytrust）：
```
    uint256 public totalDelayedMessagesRead;

    IBridge public bridge;

    /// @dev The size of the batch header
    uint256 public constant HEADER_LENGTH = 40;
    /// @dev If the first batch data byte after the header has this bit set,
    /// the sequencer inbox has authenticated the data. Currently not used.
    bytes1 public constant DATA_AUTHENTICATED_FLAG = 0x40;

    IOwnable public rollup;
    mapping(address => bool) public isBatchPoster;
    ISequencerInbox.MaxTimeVariation public maxTimeVariation;
    mapping(bytes32 => DasKeySetInfo) public dasKeySetInfo;
```
由于sequencerInbox是用于存放da信息的，因此同样也对应如何将数据'存放'入合约，当然，该存入并不是指写入合约状态，而是通过calldata的方式进行保存，这种方式虽然无法被其他合约链上读取，但是可以极大的减少da的开销。以下我们将介绍2种将da写入合约的方式：

## 1. sequencer直接写入batch
以下函数是inbox合约中最常被使用到的函数 `addSequencerL2Batch`, 该函数被sequencer用于添加新的交易batch。
```
function addSequencerL2Batch(
        uint256 sequenceNumber,
        bytes calldata data,
        uint256 afterDelayedMessagesRead,
        IGasRefunder gasRefunder
    ) external override refundsGas(gasRefunder) {
        if (!isBatchPoster[msg.sender] && msg.sender != address(rollup)) revert NotBatchPoster();

        (bytes32 dataHash, TimeBounds memory timeBounds) = formDataHash(
            data,
            afterDelayedMessagesRead
        );
        // 将calldata长度设置为0是因为调用者非该交易的起发者，因此它可能没有为该input的calldata花费。
        (
            uint256 seqMessageIndex,
            bytes32 beforeAcc,
            bytes32 delayedAcc,
            bytes32 afterAcc
        ) = addSequencerL2BatchImpl(dataHash, afterDelayedMessagesRead, 0);
        if (seqMessageIndex != sequenceNumber)
            revert BadSequencerNumber(seqMessageIndex, sequenceNumber);
        emit SequencerBatchDelivered(
            sequenceNumber,
            beforeAcc,
            afterAcc,
            delayedAcc,
            afterDelayedMessagesRead,
            timeBounds,
            BatchDataLocation.SeparateBatchEvent
        );
        emit SequencerBatchData(sequenceNumber, data);
}
```

## 2.通过delayed inbox写入
除了最常用的sequencer直接写入batch以外，用户还可以选择发送忘delayed inbox并在间隔期（Sequencer-only window，目前是1天）过后将其forceInclusion，下面是该代码解析。
```
    /// @inheritdoc ISequencerInbox
    function forceInclusion(
        uint256 _totalDelayedMessagesRead,
        uint8 kind,
        uint64[2] calldata l1BlockAndTime,
        uint256 baseFeeL1,
        address sender,
        bytes32 messageDataHash
    ) external {
        //检查读取是否正确
        if (_totalDelayedMessagesRead <= totalDelayedMessagesRead) revert DelayedBackwards();
        bytes32 messageHash = Messages.messageHash(
            kind,
            sender,
            l1BlockAndTime[0],
            l1BlockAndTime[1],
            _totalDelayedMessagesRead - 1,
            baseFeeL1,
            messageDataHash
        );
        // 需要在Sequencer-only window结束后才可forceInclusion
        if (l1BlockAndTime[0] + maxTimeVariation.delayBlocks >= block.number)
            revert ForceIncludeBlockTooSoon();
        if (l1BlockAndTime[1] + maxTimeVariation.delaySeconds >= block.timestamp)
            revert ForceIncludeTimeTooSoon();

        // 检查message hash是否代表未被确认的首个delayed交易
        bytes32 prevDelayedAcc = 0;
        if (_totalDelayedMessagesRead > 1) {
            prevDelayedAcc = bridge.delayedInboxAccs(_totalDelayedMessagesRead - 2);
        }
        if (
            bridge.delayedInboxAccs(_totalDelayedMessagesRead - 1) !=
            Messages.accumulateInboxMessage(prevDelayedAcc, messageHash)
        ) revert IncorrectMessagePreimage();

        (bytes32 dataHash, TimeBounds memory timeBounds) = formEmptyDataHash(
            _totalDelayedMessagesRead
        );
        // 将该交易并入合约中
        (
            uint256 seqMessageIndex,
            bytes32 beforeAcc,
            bytes32 delayedAcc,
            bytes32 afterAcc
        ) = addSequencerL2BatchImpl(dataHash, _totalDelayedMessagesRead, 0);
        emit SequencerBatchDelivered(
            seqMessageIndex,
            beforeAcc,
            afterAcc,
            delayedAcc,
            totalDelayedMessagesRead,
            timeBounds,
            BatchDataLocation.NoData
        );
    }
```
关于如何调用以及获得相应参数，欢迎查看[Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk/blob/c-nitro-migration/src/lib/inbox/inbox.ts#L102)

注意，虽然在以上合约中有处理消息信息，但消息本身没有被写入状态，仅仅只是通过calldata记录。