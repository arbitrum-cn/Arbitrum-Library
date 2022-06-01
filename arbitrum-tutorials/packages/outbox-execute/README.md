# Outbox Demo


Outbox合约负责接收和执行所有“Outgoing”消息；即，从 Arbitrum 传递到以太坊的消息。

（预期的）最常见的用例是提款（例如，以太币或其他erc20代币），但Outbox可以处理任何合约调用，如本演示所示。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

## 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### 运行demo

```
 yarn outbox-exec --txhash 0xmytxnhash
```


- _0xmytxnhash_ 是触发L2->L1消息的L2交易哈希。

### 更多信息

欢迎查看我们的[跨层消息文档](https://developer.offchainlabs.com/docs/l1_l2_messages).

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
