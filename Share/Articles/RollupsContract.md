# rollup合约
## 什么是Rollups合约
- Validator：
    - 主动验证者：
通过提议新的Rollup区块来推进Rollup链的状态，由于提议Rollup区块需要质押，因此活跃的验证者需要一直提供质押，一条链只需要一个诚实的活跃验证者就可以不断推进Rolllup链的状态更新，因为它将始终会质押在正确的状态上面，当有攻击者质押错误状态的时候就会触发挑战。

    - 防御验证者：
防御型验证者并不主动参与Rollup链的状态更新，而是监视着Rollup协议的运行，如果主动验证者所提出的Rollup区块都是正确的，那么防御验证者不会参与网络。但是如果有恶意验证者提议了不正确的Rollup区块，那么防御验证者将会自行提议正确区块或者在其他已经提议的正确区块上面质押来干预。最后通过Rollups合约中的Challenge合约来裁决。在这个过程中，是存在经济博弈的，即作恶验证者需要质押资产才能提议新的Rollup区块，如果其质押了错误的状态，那么防御验证者可以通过质押正确的状态来获得作恶验证者被罚没的质押作为奖励。同时，为了防止作恶验证者和正义验证者串通进行无成本的作恶，正义验证者在接受作恶验证者的质押作为奖励时，仅能获得作恶验证者被罚没资产的一半。

    - 瞭望塔验证者：
瞭望台验证者从不质押，它只是监视Rollup协议网络，如果有恶意验证者提议了不正确的区块，瞭望塔验证者会发出警告，比如告知防御验证者去质押正确的状态来获取作恶验证者的质押作为奖励。

## Rollups区块
由验证者打包生成的区块，主要信息为Rollups相关信息，比如挑战根，L2->L1的消息根，质押信息等。

## Arbitrum Rollup区块
Rollups系列合约主要负责对Arbitrum上的Rollups区块，L2->L1的Withdraw交易以及欺诈证明负责。[合约文件夹传送门](https://github.com/OffchainLabs/nitro/blob/master/contracts/src/rollup)
- Node工厂合约（Nitro升级之后为lib）：
用于创建Node合约
- Node合约（Nitro升级之后为Struct）：
即Rollup区块，需要注意的是每个Rollups区块都有一个对应的合约。
- Challenge工厂合约：
用于创建Challenge合约。
- Challenge合约：
发生Challenge的合约，每个Challenge对应一个合约
- Rollup合约：
管理rollup协议，调度用户对于rollup系列操作的调用

Arbitrum Rollup合约
Rollups主要函数
·CreateNewNode
创建新的rollup区块需要调用的函数。
```
 function createNewNode(
        RollupLib.Assertion calldata assertion,
        uint64 prevNodeNum,
        uint256 prevNodeInboxMaxCount,
        bytes32 expectedNodeHash
    ) returns (bytes32 newNodeHash)
```

·CreateNewStake
网络加入一个新的验证者进行质押存款需要调用的函数
```
function createNewStake(address stakerAddress, uint256 depositAmount)
```

·ConfirmNode
七天挑战期结束以后，确定一个区块内交易合法需要调用的函数。
```
function confirmNode(
        uint64 nodeNum,
        bytes32 blockHash,
        bytes32 sendRoot
    ) internal
```

