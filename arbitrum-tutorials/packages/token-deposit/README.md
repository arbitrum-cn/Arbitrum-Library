# token-deposit Tutorial

`token-deposit` 演示了代币如何从以太坊（第 1 层）移动到 Arbitrum（第 2 层）链，该方法使用的是标准Token网关方法，该方法使用于标准逻辑实现的ERC20代币。

有关它如何在后台工作的信息，请参阅我们的 [token bridging docs](https://developer.offchainlabs.com/docs/bridging_assets)。
#### **标准ERC20 Deposit**

我们将通过我们的 Arbitrum 代币桥以将 ERC20 代币存入 Arbitrum 链。

在这里，我们部署一个 [demo token](./contracts/DappToken.sol) 并触发存款；默认情况下，存款将通过标准 ERC20 网关路由，在初始存款时，标准 arb ERC20 合约将自动部署到 L2。


我们使用 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 库来启动和验证存款。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### 运行Demo:

```
yarn run token-deposit
```

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
