# Inbox
Inbox是用于存放l2 da（数据可用性）的合约，该合约保证了l2上的数据可用性，数据可用性是确保l2安全最关键的一环，其原因在于该da包含了l2上交易基本的信息，见[sequencerMessage](./sequencerMessage.md),并且这些交易也按照顺序进入了inbox,因此后续任何人可以通过读取l1中的inbox获得历史交易摘要，并将这些交易重新执行，便可获得l2上最新的状态，因此任何人都可以验证l2上的状态，并且只需要有一人诚实，用户便可获得正确的状态（或者用户自行运行node来验证）。

这一切使得l2中的安全保障能控制为1/n（即网络中有n个节点，我们只需要1个诚实，结果便可正确），而这一切最关键的地方则是l1上的inbox合约，下面，我们将解析inbox合约处理消息的过程：

## Sequencer Inbox
在inbox里，我们可以看到有以下变量，其中包含了inbox中消息的摘要汇总，bridge合约地址，：
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
```
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
        // we set the calldata length posted to 0 here since the caller isn't the origin
        // of the tx, so they might have not paid tx input cost for the calldata
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