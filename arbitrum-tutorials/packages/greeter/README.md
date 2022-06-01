# Greeter Tutorial


`greeter` 是 Arbitrum  L1 到 L2 消息传递系统（又名“可重试票证”）的简单演示。

它部署了 2 个合约——一个到 L1，另一个到 L2，并让 L1 合约向 L2 合约发送消息以自动执行。

脚本和合约演示了如何与 Arbitrum 的核心桥接合约交互以创建这些可重试消息、如何计算并将适当的费用从 L1 转发到 L2 以及如何使用 Arbitrum 的 L1-to-L2 消息 [地址别名](https://developer.offchainlabs.com/docs/l1_l2_messages#address-aliasing)。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多.

点击[这里](https://developer.offchainlabs.com/docs/l1_l2_messages)以获取可重试票据的更多相关信息。

### 运行Demo:

```
yarn run greeter
```

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)


<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
