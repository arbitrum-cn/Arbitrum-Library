# eth-withdraw Tutorial



`eth-withdraw` 展示了如何将Ether从 Arbitrum（第 2 层）转移到以太坊（第 1 层）链中。

请注意，此 repo 涵盖了初始化交易和 Ether withdraw；有关（稍后）从Outbox合约释放资金的演示，请参阅 [outbox-execute](../outbox-execute/README.md)

### 它是如何工作的（底层）

为了从 Arbitrum 提取 Ether，客户端使用“ArbSys”接口创建 `L2->L1` 消息，并在稍后允许他们从 L1 Bridge.sol 合约中的中释放 Ether。有关详细信息，请参阅 [Outgoing messages文档](https://developer.offchainlabs.com/docs/l1_l2_messages#l2-to-l1-messages-lifecycle)。


_注意: 执行脚本将需要您的 L2 帐户资金中至少有0.000001 Eth._

### **使用Arbitrum SDK工具集**

我们的 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 提供了一种提取 Ether 的简单方法，使得客户端不需要手动连接任何合约。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

### 运行Demo

```
yarn run withdrawETH
```

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

---

### 想要查看在Arbitrum链上的输出?


脚本成功执行后，您可以前往 [Arbitrum 区块浏览器](https://rinkeby-explorer.arbitrum.io/#)，输入您的地址，查看您的 Layer 2 余额中扣除的 ETH 数量.请注意，您的第 1 层余额只会在rollups的争议期结束后更新。
<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
