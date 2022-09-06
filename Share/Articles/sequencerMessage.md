# 详解如何从Arbitrm sequencer消息中获得交易信息
Arbitrum中的sequencer可以定序用户交易，使得交易能瞬时确认。除此之外，sequencer还可作为其他全节点的feed器，全节点只需要链接到sequencer，并不断的舰艇feed信息，即可同步arbitrum网络中的状态。本文将从feed信息中详解full node在收取到feed信息后是如何从中提取交易信息的一些过程（目前本文仅适用于classic节点交易）：

为了更好的获得sequencer的feed消息，您可以直接使用指令：
`wscat -c wss://arb1.arbitrum.io/feed` 获得feed信息的订阅，下方是我们随便运行后得到的一组交易feed：
```
{"version":1,"messages":[{"feedItem":{"batchItem":{"lastSequenceNumber":39222805,"accumulator":[108,210,195,226,50,56,98,129,148,138,62,160,76,252,31,139,100,165,212,167,167,148,87,184,236,228,222,167,22,5,101,87],"totalDelayedCount":706410,"sequencerMessage":"A6SxCsYeeeoeFQ33C43aUzkZKP0UAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADoeB0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYuNZ6gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACVn4VAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADgYcH/w+EDzXaAYMJzUqUq7xfmWOcm2vLWFRN3wTvpoAvQGSAOMdN2QAAAAAAAAAAAAAAAD1rozHj2XAsXoqNJU5diihfIjq63WQYni+oBa5ulNyUj538KZKnESOcjMyx3+hCzPRKVQpMPjSyX3CYbSaTJE/vSfPBrpJC35xDd6Vf2hQEHLYSjgA="},"prevAcc":[113,217,121,246,71,90,245,191,67,243,101,177,64,209,218,90,128,11,70,215,226,234,81,155,143,138,221,144,180,76,6,243]},"signature":"csq6VAl0XDgqk26G9qLkiLQeYBYWfjSMFRanT1qROKUUw55+MeqDNzjG4LN8aJ8W0CvSFd+II59qDTdasqWZRQA="}],"confirmedAccumulator":{"isConfirmed":false,"accumulator":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]}}
```
该消息对应Arbitrum sequencer的[BroadcastMessage](https://github.com/OffchainLabs/arbitrum/blob/6c2d42e251c764859813db71c774dede3d00a289/packages/arb-util/broadcaster/types.go#L39),我们先来看看它的strcut结构：
```
type BroadcastMessage struct {
	Version              int                     `json:"version"`
	Messages             []*BroadcastFeedMessage `json:"messages"`
	ConfirmedAccumulator ConfirmedAccumulator    `json:"confirmedAccumulator"`
}
```
可以看到，其中有三项，第一项为版本号，第二项为sequencer的消息信息。第三项为状态集成哈希。为了探解定序器消息信息，我们继续解剖第二项Message如下，并取得其中的SequencerFeedItem如下：
```
type SequencerBatchItem struct {
	LastSeqNum        *big.Int    `json:"lastSequenceNumber"`
	Accumulator       common.Hash `json:"accumulator"`
	TotalDelayedCount *big.Int    `json:"totalDelayedCount"`
	SequencerMessage  []byte      `json:"sequencerMessage"`
}
```
在该struct里，主要是和inbox交易序列有关的信息，而交易信息则主要在`SequencerMessage`里，因此我们继续查找得到该struct：
```
type InboxMessage struct {
	Kind        Type
	Sender      common.Address
	InboxSeqNum *big.Int
	GasPrice    *big.Int
	Data        []byte
	ChainTime   ChainTime
}
```
同时该strcut也对应着刚刚获得的feed消息中的sequencerMessage：
```
A6SxCsYeeeoeFQ33C43aUzkZKP0UAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADoeB0AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAYuNZ6gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACVn4VAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADgYcH/w+EDzXaAYMJzUqUq7xfmWOcm2vLWFRN3wTvpoAvQGSAOMdN2QAAAAAAAAAAAAAAAD1rozHj2XAsXoqNJU5diihfIjq63WQYni+oBa5ulNyUj538KZKnESOcjMyx3+hCzPRKVQpMPjSyX3CYbSaTJE/vSfPBrpJC35xDd6Vf2hQEHLYSjgA="
```
关直接看这串内容是无法获得任何信息的，这是因为该数据使用过base64编码，因此可以先使用base64解码器进行解码，获得数据如下：
```
03a4b10a c61e79ea 1e150df7 0b8dda53 391928fd 14000000 00000000 00000000 00000000 00000000 00000000 00000000 0000e878 1d000000 00000000 00000000 00000000 00000000 00000000 00000000 0062e359 ea000000 00000000 00000000 00000000 00000000 00000000 00000000 0002567e 15000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00038187 07ff0f84 0f35da01 8309cd4a 94abbc5f 99639c9b 6bcb5854 4ddf04ef a6802f40 648038c7 4dd90000 00000000 00000000 00003d6b a331e3d9 702c5e8a 8d254e5d 8a285f22 3abadd64 189e2fa8 05ae6e94 dc948f9d fc2992a7 11239c8c ccb1dfe8 42ccf44a 550a4c3e 34b25f70 986d2693 244fef49 f3c1ae92 42df9c43 77a55fda 14041cb6 128e00
```
当然，为了更好的查看数据顺序，大家也可以先参考[该函数](https://github.com/OffchainLabs/arbitrum/blob/6c2d42e251c764859813db71c774dede3d00a289/packages/arb-util/inbox/inboxMessage.go#L71) 尝试提取其中数据。

1.首先，我们获得第一个byte为0x03，该数据为[message type](https://developer.offchainlabs.com/docs/arbos_formats#incoming-messages)

2.第二个数据为`0xa4b10ac61e79ea1e150df70b8dda53391928fd14`,在struct里该数据为`sender`（20bytes）信息，由于是sequencer发送到l1的，因此该sender也为sequencer自己的地址信息。

3.第三部我们提取到`blocknumber`（32bytes）和`timestamp`（32bytes）信息分别为：`0xe8781d`和 `0x62e359ea`，即l1的区块高度和区块时间。

4.第四部继续提取其中的`inboxSeqNumber`（32bytes）信息，为`0x2567e15`，该数据的作用是标明该交易在inbox中的位置，以确定交易发生序号，这也是sequencer最主要的功能之一：为交易定序。

5.第五部提取交易的`gasPrice`（32bytes）信息，为`0x0`。

6.该部分我们将获得交易中的rlp编码后的data信息，拆分如下:

```
1. 0f 为交易nonce

2. 84 为 0x84 - 0x80 = 4 bytes

    获得对应后4bytes的数据为 0x0f35da01 （gasPrice）

3. 83 为 0x83 - 0x80 = 3 bytes

    获得对应后3bytes的数据为0x09cd4a （gas）

4. 94 为 0x94 - 0x80 = 20 bytes

    获得后20bytes的数据为0xabbc5f99639c9b6bcb58544ddf04efa6802f4064 （to）

5. 80 为 0x80 - 0x80 = 0 bytes, 也就是value为0

6. 该数据为交易的data数据：0x38c74dd90000000000000000000000003d6ba331e3d9702c5e8a8d254e5d8a285f223aba
    首先获得前4bytes的数据0x38c74dd9为函数名，后32bytes数据为：0000000000000000000000003d6ba331e3d9702c5e8a8d254e5d8a285f223aba

7. 交易签名数据r: 0xdd64189e2fa805ae6e94dc948f9dfc2992a711239c8cccb1dfe842ccf44a550a

8. 交易签名数据s: 0x4c3e34b25f70986d2693244fef49f3c1ae9242df9c4377a55fda14041cb6128e
```
由此，我们便获得了该交易的交易信息。该交易为：[0x50e6abe5e160358a71445457382b7b23b90c4199845396fda627c59f2f598732](https://arbiscan.io/tx/0x50e6abe5e160358a71445457382b7b23b90c4199845396fda627c59f2f598732),我们使用`eth_getTransactionByHash`获得信息如下:
```
{"jsonrpc":"2.0","id":1,"result":{"blockHash":"0x429f9272a4e3cd4bc9fbaadebf4001fcff00a094e1a6d56b64e12134efa2d8ae","blockNumber":"0x11f6f3c","from":"0x3abce4e29d700a3915209fdc46c81ffead2f3d5c","gas":"0x9cd4a","gasPrice":"0xf35da01","hash":"0x50e6abe5e160358a71445457382b7b23b90c4199845396fda627c59f2f598732","input":"0x38c74dd90000000000000000000000003d6ba331e3d9702c5e8a8d254e5d8a285f223aba","nonce":"0xf","to":"0xabbc5f99639c9b6bcb58544ddf04efa6802f4064","transactionIndex":"0x0","value":"0x0","v":"0x14986","r":"0xdd64189e2fa805ae6e94dc948f9dfc2992a711239c8cccb1dfe842ccf44a550a","s":"0x4c3e34b25f70986d2693244fef49f3c1ae9242df9c4377a55fda14041cb6128e","l1SequenceNumber":"0x2567e15","parentRequestId":"0x4f2c69785ac02ebe59d6804bbfd1752ca2e5e8161e80edf39e5e5a33a2e87d08","indexInParent":"0x0","arbType":"0x3","arbSubType":"0x4","l1BlockNumber":"0xe8781d"}}
```
如上，我们将交易对应回去，发现信息全部吻合，因此，我们的sequencer交易解码成功。

## 查看交易在l1中的信息

得到交易后，前往arbiscan可查看更多信息，我们找到[Submission Tx Hash](https://etherscan.io/tx/0x1f39f0f26d6f4823dfc6860fb53a8a7e80703c41dd69577b4be97c085b681037)，在该界面中，我们可以在`input data`中找到刚刚获得的sequencer消息（不包含sender，可使用data信息辅助查找）。