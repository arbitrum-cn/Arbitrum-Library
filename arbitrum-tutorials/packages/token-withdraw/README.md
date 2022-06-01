# token-withdraw Tutorial

`token-withdraw` 展示了如何将 ERC20 代币从 Arbitrum（第 2 层）转移到以太坊（第 1 层）。

请注意，此 repo 涵盖了启动令牌提取；有关（稍后）从发件箱释放资金的演示，请参阅 [outbox-execute](../outbox-execute/README.md)
### 它是如何工作的？

要从 Arbitrum 提取代币，网关合约会发送一条消息，该合约会在 L2 上烧掉代币，并向 L1 发送一条消息，一旦争议期结束，该代币就可以从托管中释放。有关详细信息，请参阅 [传出消息文档](https://developer.offchainlabs.com/docs/l1_l2_messages#l2-to-l1-messages-lifecycle)。

---

#### **Standard ERC20 Withdrawal**

在这个Demo中，我们部署了一个新的代币，然后将一些该代币存入 L2。然后，我们使用这些新代币触发提款到 L1。

我们使用 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 库进行token桥交互。

See [./exec.js](./scripts/exec.js) for inline explanation.

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### 运行Demo

```
yarn withdraw-token
```

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
