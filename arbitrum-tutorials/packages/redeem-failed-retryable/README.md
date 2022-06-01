# Redeem Failed Retryable Tickets Tutorial


可重试票据是 Arbitrum 协议的规范方法，用于将通用消息从以太坊传递到 Arbitrum。可重试票证是由 L1 编码和传递的 L2 消息；如果提供了gas，将立即执行。如果没有提供gas或执行失败，它将被放置在L2重试缓冲区中，任何用户都可以在规定时间内（大约一周）重新尝试执行。
您可以使用 `exec-createFailedRetryable` 脚本来创建一个会失败的可重试票证，然后使用 `redeem-failed-retryable` 向您展示如何赎回（重新执行）位于 L2 重试缓冲区中的票证。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多。

### 运行 Demo:

创建一个会失败的可重试票据:

```
 yarn run createFailedRetryable
```

赎回刚刚失败后的票据:

```
 yarn redeemFailedRetryable --txhash 0xmytxnhash
```

- _0xmytxnhash_ 是触发 L1->L2 消息的L1交易哈希。

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### More info

有关可重试票证的更多信息，请参阅我们的 [关于跨层消息传递的开发人员文档](https://developer.offchainlabs.com/docs/l1_l2_messages)。

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
