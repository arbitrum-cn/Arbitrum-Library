# custom-token-bridging Tutorial


有一些特殊的Token需要除了StandardERC20网关之外的功能（如算法稳定币等）。因此`custom-token-bridging` 展示了如何设置这些自定义token来注册到我们的官方桥里。

如需了解更多关于Arbitrum bridge的信息, 欢迎查看 [token bridging docs](https://developer.offchainlabs.com/docs/bridging_assets).

#### **使用Generic-Custom网关来使得自定义Token跨链**

将自定义令牌桥接到 Arbitrum 链是通过 Arbitrum Generic-Custom 网关完成的。我们的Generic-Custom网关的设计足够灵活，可以满足大多数（但不一定是全部）自定义erc20代币的需求。



在这里，我们在 L1 上部署了一个 [demo custom token](./contracts/L1Token.sol)，在 L2 上部署了一个 [demo custom token](./contracts/L2Token.sol)。然后，我们使用 Arbitrum 自定义网关合约将我们的 L1 自定义令牌注册到我们的 L2 自定义令牌。将令牌注册到自定义网关后，我们将 L1 令牌注册到 L1 上的 Arbitrum 网关路由器。

我们使用 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 库来初始化并验证跨桥操作.

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多.
### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### 运行:

```
yarn run custom-token-bridging
```

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
