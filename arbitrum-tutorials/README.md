# Arbitrum Tutorials



这个 monorepo 将帮助您开始在 Arbitrum 上进行构建。它提供了各种简单的演示来展示和解释如何与 Arbitrum 交互——直接在 L2 上部署和使用合约，在 L1 和 L2 之间转移 Ether 和代币等等。

为了方便起见，我们展示了如何使用广泛支持的以太坊生态系统工具（Hardhat、Ethers-js 等）以及我们特殊的 [Arbitrum SDK](https://github.com/OffchainLabs/arbitrum-sdk)。

## Installation

从 root 目录:

```bash
yarn install
```

## 包含了哪些信息?

#### :white_check_mark: 基础使用

- 🐹 [Pet Shop DApp](./packages/demo-dapp-pet-shop/) (L2 only)
- 🗳 [Election DApp](./packages/demo-dapp-election/) (L2 only)

#### :white_check_mark: 资产转移

- ⤴️ 🔹 [Deposit Ether](./packages/eth-deposit/)
- ⤵️ 🔹 [Withdraw Ether](./packages/eth-withdraw/)
- ⤴️ 💸 [Deposit Token](./packages/token-deposit/)
- ⤵️ 💸 [Withdraw token](./packages/token-withdraw/)

#### :white_check_mark: 基本操作

- 🤝 [Greeter](./packages/greeter/) (L1 to L2)
- 📤 [Outbox](./packages/outbox-execute/) (L2 to L1)

#### :white_check_mark: 高级特性

- ®️ [Arb Address Table](./packages/address-table/)
- 🌉 [Bridging Custom Token](./packages/custom-token-bridging/)

<p align="center">
  <img width="350" height="100" src= "https://arbitrum.io/wp-content/uploads/2021/01/cropped-Arbitrum_Horizontal-Logo-Full-color-White-background-scaled-1.jpg" />
</p>
