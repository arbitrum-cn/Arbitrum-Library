

## L1至L2信息传递
### Retryable(可重试)

可重试票据是Arbitrum创建L1到L2消息时，使用的最常见的方法——即在L1交易记录中开启一个在L2执行的消息,并且该消息可以重复执行（在有效期内）直到执行成功。一个可重试票可以按固定的费用 (只取决于它的calldata大小) 在L1提交；它在L1的提交与它在L2的执行是可分离且异步的。Retryable提供了跨链操作之间的原子性(atomicity)；如果请求提交的L1交易成功了(即没有恢复)，那么Retryable在L2的执行也有强大的保证，最终也会成功。

一个Retryable是通过「Inbox」的「createRetryableTicket」方法提交的。在常见的情况下，Retryable的提交后会有一次执行交易的尝试（即 "自动兑换"）。如若尝试失败，或在Retryable提交后没有安排，任何人都可以通过调用「ArbRetryableTx」预编译的兑换(redeem)方法，尝试兑换它。请求兑换的一方提供将用于执行Retryable的gas费用。如果执行Retryable成功，Retryable会被删除。如果执行失败，Retryable将继续存在，可以进一步尝试兑换。如果一个固定的时间段(目前是一周)过去了而没有成功兑换，Retryable就会过期并被[自动丢弃](https://github.com/OffchainLabs/nitro/blob/fa36a0f138b8a7e684194f9840315d80c390f324/arbos/retryables/retryable.go#L262)，除非有人支付了费用将Retryable再[续上](https://github.com/OffchainLabs/nitro/blob/fa36a0f138b8a7e684194f9840315d80c390f324/arbos/retryables/retryable.go#L207)一段时间。只要每次在过期之前续费，Retryable可以无限期存在。


### 提交Retryable


提交一次Retryable要做到以下：

- 创建一个新的Retryable，包含caller、dest（目标地址）、callvalue、calldata和提交交易的受益人
- 从caller那里扣除资金以支付call费(像往常一样)，并将其放入托管(escrow)，以便以后用于兑换Retryable
- 为Retryable分配一个特殊的「TicketID」
- 使「ArbRetryableTx」预编译的合同发出一个包含TicketID「TicketCreated」事件
- 如果提交的交易包含gas，则安排对新的Retryable进行兑换，使用提供的gas，就像[「ArbRetryableTx」](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx)预编译的[兑换](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx)方法被调用一样。


在大多数情况下，提交者将提供gas，并希望能立即兑换成功，如果立即兑换失败，以后的重试只能作为一种备份机制。(例如：因为L2 gas价格意外地上涨——它可能会失败）。通过这种方式，L1合约可以向L2提交交易，交易通常会在L2立即运行，但允许任何一方进行重试——如果前一笔交易失败。

当Retryable被兑换时，它将以原始提交的发送者、目的地、callvalue和calldata执行。为了这个目的，在最初提交Retryable时，callvalue将被托管。如果一个有callvalue的Retryable最终被丢弃(它从未成功运行过)，托管的callvalue将被支付给初始提交时指定的 "受益人 "账户。

一个Retryable的受益人有特殊的权力来[取消](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx)Retryable。这与Retryable计时结束的效果相同。


### 兑换一个Retryable

如果在提交时没有进行兑换，或者提交的初始兑换失败，任何人都可以通过调用[「ArbRetryableTx」](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx)的[兑换](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx)预编译方法，再次尝试兑换Retryable，该方法将调用的gas捐赠给下一次尝试。ArbOS将把这个兑换（它是它自己的特殊ArbitrumRetryTx类型）[排列](https://github.com/OffchainLabs/nitro/blob/fa36a0f138b8a7e684194f9840315d80c390f324/arbos/block_processor.go#L245)到它的兑换表中，ArbOS会确保在转到它形成的区块中的下一个非兑换交易之前[用完](https://github.com/OffchainLabs/nitro/blob/fa36a0f138b8a7e684194f9840315d80c390f324/arbos/block_processor.go#L135)。通过这种方式，兑换被安排在尽可能短的时间窗口内发生，并且总是与安排它的交易在同一个区块中。请注意：兑换尝试的gas来自于对兑换的调用，所以在执行之前不会达到区块的gas限制。

在成功的状况下，「To」地址保持托管的callvalue，任何未使用的gas会被返还到ArbOS的gas池。失败时，callvalue将返回给下一个兑换者的托管。在这两种情况下，网络费用都是在调度交易期间支付的，所以不收取费用，也不退还费用。

在兑换Retryable的过程中，试图取消同一Retryable，或安排同一Retryable的另一次兑换，将会被退回。在这种方式下，Retryable程序是不可自我修改的。


### 收据(Receipts) 

一个Retryable的收据的生命周期中会发出两种类型的L2交易收据：

票据创建收据。这种收据表示已经成功创建了一张Retryable票据；任何对「Inbox」的「createRetryableTicket」L1调用方法的成功都保证会生成一张票据。票据创建收据包括一个「TicketCreated」事件(来自[ArbRetryableTx](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx))，它将包括一个「ticketId」。这个「ticketId」可以通过RLP编码和散列交易来计算；见[calculateSubmitRetryableId](https://github.com/OffchainLabs/arbitrum-sdk/blob/6cc143a3bb019dc4c39c8bcc4aeac9f1a48acb01/src/lib/message/L1ToL2Message.ts#L109)。

兑换尝试。兑换尝试收据代表Retryable票据的L2执行尝试的结果。它包括一个来自 [ArbRetryableTx](https://developer.offchainlabs.com/arbos/precompiles#ArbRetryableTx) 的「RedeemScheduled」事件，有一个「ticketId」字段。对于一个给定的票据，最多只能有一次成功的兑换尝试；打比方说，如果初始创建时的自动兑换成功了，那么对于该票据，只有自动兑换的收据会被发出；如果自动兑换失败(或从未尝试过——即所提供的L2 gas限额*L2 gas价格=0)，每次初始尝试都会发出一张兑换尝试收据，直到一次成功为止。

### 替代的 "不安全 "Retryable票据创建

「Inbox.createRetryableTicket」的“便利方法”，包括理智检查(sanity checks)，以帮助最大限度地减少用户出错的风险：该方法将确保直接从L1提供足够的资金，以支付当前票据创建/执行的成本。它还将把所提供的受益人或信用回馈地址转换为其地址别名(见下文)，如果这两个地址是同一个合同的话，就会为L1合同提供一条回收资金的路径。权威用户可以通过「Inbox」的「unsafeCreateRetryableTicket」方法绕过这些理智检查措施；正如该方法的名字在不断地尝试警告你的那样，只有真正清楚自己在做什么的用户才能访问它。

### ETH Deposit

有一种特殊的消息类型用于简单的ETH Deposit；即从L1到L2跨入ETH。ETH可以通过调用「Inbox」的「depositETH」方法来存入。如果L1的调用者是EOA，ETH将被存入L2的同一个EOA地址；如果L1的调用者是一个合约，资金将被存入合约的别名地址(见下文)。

注意，通过「depositETH」将ETH存入L2上的合约，不会触发合约的fallback功能。

原则上，Retryable票据也可以用来存入ETH；如果需要更灵活的目标地址，或者想在L2端触发fallback功能，这可能是比特殊的ETH-deposit消息类型更可取的方案。


### 通过延时收件箱(Delayed Inbox)进行交易

虽然Retryables和ETH Deposit必须通过延迟收件箱提交，但原则上，任何信息都可以通过这种方式提交(这是一个必要的手段)，以确保Arbitrum链在序列器出现问题时，也能保留审查阻力(见序列器和审查阻力)。然而，在普通/happy的情况下，期望/建议客户只对Retryables和ETH Deposit使用延迟收件箱，而对所有其他信息通过序列器进行交易。


### 地址别名(Address Aliasing)

所有通过延迟收件箱提交的信息，其发件人的地址都是 "别名"(aliased)的：当这些无符号信息在L2上执行时，发件人的地址——即「msg.sender」返还的地址——将不只是仅仅给L1地址发送信息；而是将该地址的 "L2别名"。一个地址的L2别名是它的值增加了十六进制值0x1111000000000000000000000000000000001111：

`L2_Alias = L1_Contract_ Address + 0x1111000000000000000000000000000000001111 `

Arbitrum合约对「L1-到-L2」消息使用L2别名的做法，防止了跨链漏洞，如果我们简单地重复使用与L2发送者相同的L1地址，就有可能出现这种情况；也就是说，通过从L1上预期的合约地址发送Retryable的票据，来欺诈期望从给定地址进行调用的L2合同。

如果出于某种原因，你需要从链上的L2别名计算L1地址，你可以使用我们的「AddressAliasHelper」库。
```
modifier onlyFromMyL1Contract() override {
    require(AddressAliasHelper.undoL1ToL2Alias(msg.sender) == myL1ContractAddress, "ONLY_COUNTERPART_CONTRACT") 。
    _;
}
```
[原文链接](https://developer.offchainlabs.com/arbos/l1-to-l2-messaging
)