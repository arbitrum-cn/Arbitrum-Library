# eth-deposit Tutorial


`eth-deposit` 展示了如何将以太币从以太坊（第 1 层）转移到 Arbitrum（第 2 层）链中。

### 它是如何工作的（底层）

用户使用 Arbitrum 的通用 L1-to-L2 消息传递系统将 Ether 存入 Arbitrum，只需将所需的 Ether 作为调用值传递，并且无需额外数据。有关详细信息，请参阅 [可重试票证文档](https://developer.offchainlabs.com/docs/l1_l2_messages#depositing-eth-via-retryables)。

### **使用Arbitrum SDK工具集**


我们的 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 提供了一种简单便捷的 Ether 存款方法，消除了客户端手动连接任何合约的需要。


可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

To run:

```
yarn run depositETH
```

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

---

脚本执行成功后，您可以前往 [Arbitrum 区块浏览器](https://rinkeby-explorer.arbitrum.io/#)，输入您的地址，查看分配给您地址的 ETH 数量在仲裁链上！

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
