# Address Table Demo


Address Table是 Arbitrum 上的一个预编译合约，用于注册地址，然后可以通过整数索引检索这些地址；这通过最小化用户address所需要占用的calldata数据来节省gas开销。


这个demo展示了一个简单的合约，它可以通过地址表中的索引从合约中检索地址，以及一个可以从客户端处提前注册地址的函数（如果有需要的话）。

可以查看 [./exec.js](./scripts/exec.js) 里的注释以了解更多.
### 运行demo

```
 yarn run exec
```

### 配置环境变量

在 `.env-sample` 中设置参数. 并将其复制为 `.env` 文件:

```bash
cp .env-sample .env
```

(你仍然需要设置一些参数, 比如 `DEVNET_PRIVKEY`)

### More info

查看我们的[开发者文档](https://developer.offchainlabs.com/docs/special_features)以了解更多。

<p align="center"><img src="../../assets/offchain_labs_logo.png" width="600"></p>
