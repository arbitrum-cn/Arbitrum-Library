## Arbitrum One 升级到 Nitro 期间节点操作员的说明

_tl;dr — 当我们在 8 月 31 日将 Arbitrum One 迁移到 Nitro 时，节点操作员需要遵循的一组快速说明。_
在 Arbitrum One 迁移到 Nitro 之后，Arbitrum Classic 节点将只提供对经典链（pre-nitro）状态的查询，并且为了正确同步迁移的链，任何运行节点的人都需要运行 Nitro 节点。在这里，我们列出了相应时间/事件要遵循的步骤，以确保您已设置好运行 Nitro 节点。

### 8 月 31 日 Nitro 迁移之前或期间

**1) 下载最新的 Nitro 节点版本 v2.0.0**
- [docker image链接](https://hub.docker.com/r/offchainlabs/nitro-node/tags?page=1&name=v2.0.0-292bbc4)和[docker hub链接](https://hub.docker.com/r/offchainlabs/nitro-node/tags?page=1&name=v2.0.0-292bbc4)
- 成功迁移到 Nitro 后，将需要此版本才能同步链。
- **9 月 1 日后Arbitrum Goerli 和 Rinkeby 也需要运行此版本。**

**2) 使用最新版本（上面链接）启动和/或升级 Nitro 节点。**

- 在迁移之前或期间执行此操作，尽管 Arbitrum One 上的 Nitro 节点在迁移完成之前不会提供 RPC 或响应请求。
- 链 ID 将保持不变：42161。
- 您也可以参考我们的[Nitro 节点部署说明]（https://developer.offchainlabs.com/docs/Running_Nitro_Node）。

### **8 月 31 日迁移期间**

**3) 给 Nitro 节点一个种子数据库，用于初始链同步。**

- 对于种子数据库，有两个选项：
    - 选项 1 - 我们将在迁移期间执行链状态导出，完成后，我们将上传导出的副本作为种子数据库。我们会将其(数据快照)发布在我们的公共频道上，您可以在我们的 [Arbitrum Devs Twitter](https://twitter.com/ArbitrumDevs) 上关注。
    - 选项 2 - 各个操作员可以从他们自己的 Arbitrum Classic 节点导出状态，并将其用作 Nitro 节点的种子/创世数据库。但是，除非您已经提前开始了历史导出，否则这应该只用于验证我们的创世块，因为这需要一段时间。

1. 使用 `--node.rpc.nitroexport.enable` CLI 选项启动 Arbitrum Classic 节点
2.运行RPC命令`{"jsonrpc":"2.0","id":0,"method":"arb_exportHistory","params":["latest"]}`开始历史导出
3.运行RPC命令`{"jsonrpc":"2.0","id":0,"method":"arb_exportOutbox","params":["0xffffffffffffffff"]}`开始发件箱导出
4. 等待硝基迁移开始，然后创建最终块
5.运行RPC命令`{"jsonrpc":"2.0","id":0,"method":"arb_exportHistory","params":["latest"]}`完成历史导出
6. 运行RPC命令`{"jsonrpc":"2.0","id":0,"method":"arb_exportOutbox","params":["0xffffffffffffffff"]}`完成发件箱导出
7. 运行RPC命令`{"jsonrpc":"2.0","id":0,"method":"arb_exportState","params":["latest"]}`导出状态信息
8. 等待 RPC 命令 `{"jsonrpc":"2.0","id":0,"method":"arb_exportHistoryStatus","params":[]}` 返回与 `eth_blockNumber` 相同的值如果还没有
9. 等待 RPC 命令 `{"jsonrpc":"2.0","id":0,"method":"arb_exportOutboxStatus","params":[]}` 的结果停止增加（正常每秒增加一次左右）
10. 在经典节点的 db 文件夹旁边，您会找到一个 `nitroexport` 文件夹（通常位于 `~/.arbitrum/mainnet/nitroexport`）。将 `nitro` 文件夹复制到 `~/.arbitrum/arb1/nitro`
11. 使用 `nitro --l1.chain-id 1 --l2.chain-id 42161 --node.dangerous.no-l1-listener --node.l1-reader.enable=false --init 执行状态导入.then-quit --init.file ~/.arbitrum/mainnet/nitroexport/state/*/index.json`
12. 恭喜，你在 `~/.arbitrum/arb1/nitro` 有一个完整的nitro数据库！

**4) 等待链恢复和节点开始响应请求。**

- 状态导出完成后，我们将启动我们的序列器和 rpc 端点，此时整个生态系统的 Nitro 节点应该可以正常运行。

英文[原文链接](https://arbitrum.notion.site/EXTERNAL-Instructions-for-Node-Operators-During-the-Arbitrum-One-Migration-to-Nitro-bed55327954542c8939bbf7886e82ee8)