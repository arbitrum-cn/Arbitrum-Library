# custom-token-bridging Tutorial


有一些特殊的Token需要除了StandardERC20网关之外的功能（如算法稳定币等）。因此`custom-token-bridging` 展示了如何设置这些自定义token来注册到我们的官方桥里。

如需了解更多关于Arbitrum bridge的信息, 欢迎查看 [token bridging docs](https://developer.offchainlabs.com/docs/bridging_assets).

#### **使用Generic-Custom网关来使得自定义Token跨链**
Bridging a custotom token to the Arbitrum chain is done via the Arbitrum Generic-Custom gateway. Our Generic-Custom Gateway is designed to be flexible enough to be suitable for most (but not necessarily all) custom fungible token needs.
将自定义令牌桥接到 Arbitrum 链是通过 Arbitrum Generic-Custom 网关完成的。我们的Generic-Custom网关的设计足够灵活，可以满足大多数（但不一定是全部）自定义erc20代币的需求。

Here, we deploy a [demo custom token](./contracts/L1Token.sol) on L1 and a [demo custom token](./contracts/L2Token.sol) on L2. We then use the Arbitrum Custom Gateway contract to register our L1 custom token to our L2 custom token. Once done with token's registration to the Custom Gateway, we register our L1 token to the Arbitrum Gateway Rounter on L1.

在这里，我们在 L1 上部署了一个 [demo custom token](./contracts/L1Token.sol)，在 L2 上部署了一个 [demo custom token](./contracts/L2Token.sol)。然后，我们使用 Arbitrum 自定义网关合约将我们的 L1 自定义令牌注册到我们的 L2 自定义令牌。将令牌注册到自定义网关后，我们将 L1 令牌注册到 L1 上的 Arbitrum 网关路由器。

我们使用 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk) 库来初始化并验证跨桥操作.

See [./exec.js](./scripts/exec.js) for inline explanation.
可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多.
### Config Environment Variables

Set the values shown in `.env-sample` as environmental variables. To copy it into a `.env` file:

```bash
cp .env-sample .env
```

(you'll still need to edit some variables, i.e., `DEVNET_PRIVKEY`)

### Run:

```
yarn run custom-token-bridging
```

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
