# demo-dapp-election Tutorial

demo-dapp-election 是一个简单的demo示例，允许您将 Election 合约部署到 Arbitrum 并运行其功能。

合约完全依赖于 L2 且不涉及直接的 L1 交互；在该过程中，你可以感受到编写、部署和与之交互就像使用 L1 合约一样。

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### 运行Demo

```bash
yarn run exec
```

## 想要查看在Arbitrum链上的输出?

脚本执行成功后，您可以前往[Arbitrum 区块浏览器](https://rinkeby-explorer.arbitrum.io/#)，输入您的 L2 地址，即可查看 Arbitrum 链上对应的交易！

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
