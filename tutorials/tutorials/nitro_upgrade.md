# 准备好你的引擎——Nitro马上就要到来了！
言简意赅：Arbitrum One将于8月31日迁移到Nitro! 现在就准备好并测试你的合约吧! 请参考[迁移说明](https://github.com/OffchainLabs/nitro/blob/master/docs/migration/dapp_migration.md)，并关注我们的[Twitter](https://twitter.com/arbitrum)和[Discord](https://discord.com/invite/Arbitrum)了解最新情况。

一周前，我们实施了Arbitrum Rinkeby测试网向Nitro的迁移。总的来说，这个过程进展得非常顺利，现在我们正期待着Arbitrum One的主网迁移到Nitro。我们很高兴与大家分享：我们计划在8月31日进行迁移——这恰好是我们Arbitrum One首次公测版本后的一周年！

我们选择这个日期是为了确保我们的生态系统继续有时间为迁移做准备，而且我们认为这天是一个具有代表性的里程碑，能够让我们凝聚在一起——这是一个属于我们所有人的生日礼物。

## 继续为Nitro做准备
Nitro是一次重要的技术升级，它将带来整个堆栈的变化，极大地改善了在Arbitrum上的体验。我们最初在Arbitrum Goerli Nitro版本中详细介绍了这些变化，但稍微复习一下，Nitro将带来： 

- _高级Calldata压缩_，通过减少传至L1的数据量，从而进一步降低Arbitrum的交易成本。
- _以太坊L1 Gas兼容性_，使EVM的定价和核算与以太坊完全一致。
- _额外的L1互操作性_，包括与L1区块码更紧密的同步，以及对所有以太坊L1预编译的全面支持。
- 安全重试，消除了可重试票据不能被创建的故障模式。
- _Geth追踪_，提供更广泛的调试支持。
- 还有很多很多的变动。

开发人员，现在是时候确保你的合约和前端为迁移做好准备，准备好应对任何必要的变化和尽可能多的测试。如果你还没有这样做，我们强烈建议你在Arbitrum测试网中进行部署，[Arbitrum Goerli](https://developer.offchainlabs.com/docs/Public_Chains)是我们长期会使用的网络。

在我们进行迁移的过程中，我们将继续更新我们的[迁移说明](https://github.com/OffchainLabs/nitro/blob/master/docs/migration/dapp_migration.md)，当中会记录任何新出的，和具有突破性的变动，以及我们的[开发者文档](https://developer.offchainlabs.com/docs/Developer_Quickstart)。

## 在Nitro上线主网前的下一步工作
在Rinkeby顺利地迁移后，我们有信心，并已经准备好了将Arbitrum One升级至Nitro。在接下来的几周里，我们将继续与基础设施供应商、dapp和社区进行协调，以确保每个人都能充分了解即将到来的状况，并传递任何可能会出现的额外计划和更新。

在主网迁移前一周，即8月24日，我们计划进行一次影子分叉迁移——作为主网迁移前的最后彩排。影子分叉允许我们复制另一个链的状态，并运行一个平行链。它仍然接收来自主链的交易——也就是Arbitrum One。进行这一工作，然后再将影子分叉迁移到Nitro，将为我们一周后在主网上进行的迁移工作提供全面的覆盖性测试。随着时间的临近，我们会与合作伙伴团队保持充分的沟通，并携手参与影子分叉的工作。

## 周年庆
之前有提到过，我们迁移日期定在了8月31日，因为这可以让我们的开发者社区继续为迁移做准备，同时也因这与Arbitrum One上线主网的一周年的纪念日刚好吻合。

我们准备好了一些有非常趣的事情来庆祝我们的一周年庆和Nitro，并会在未来的几周内陆续分享我们的计划。

综上所述，请准备好迎接Nitro吧!

