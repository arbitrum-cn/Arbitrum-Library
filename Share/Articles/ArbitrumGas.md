# Arbitrum Gas机制
## gas计算
L1 fixed cost和L1calldata主要是放在layer1上面存档的开销，即用户的交易数据在压缩以后上传到inbox合约所需要在layer1支付的gas花销，也是二层协议的主要gas开销。
L2 computational cost和L2 storage cost是在layer2执行开销和状态变化存储的交易开销，在计算layer2交易的gas开销的时候是需要将以下四项的开销加起来计算的。

`L1 fixed cost`

`L1 calldata cost`

`L2 computational cost`

`L2 storage cost`
## gas limit
layer1的gas limit受限于区块大小，但是layer2并不是这样，Arbitrum现在的打包方式是先到先打包，所以并没有对gas limit有太多限制，而是使用了arbGas，这里的arbGas仅仅指的是L2 computational cost，可以看到每秒钟定序器能处理的arbGas limit是120K，而arbGas pool就和layer1区块的gas limit一致，每隔一段时间就会将arbGas pool加满，而不是像layer1一样更新，如果在一段时间内arbGas pool消耗完了，为了保证layer2的稳定和防止DDoS攻击，定序器就会拒绝交易。对于单笔交易的gas限制是2.4m。（仅限于Arbiitrum classic版本）
- arbGas limit per second：120K
- arbGas pool：20m
- arbGas limit per tx：2.4m
## gas estimate
layer1有JSON-RPC，layer2也有JSON-RPC，gas estimate指的是每次通过JSON-RPC发起一笔交易的交易gas费预估。由于layer2的gas开销和layer1息息相关，是L1+L2的gas花费，而且L1的gas是随时间波动的，所以有可能A时间获得的gas estimate在B时间就无法通过，所以最好是在每次发起交易前，都调用一次RPC获得新的gas estimate。
## 二维度的gas
由于arbitrum中的gas花销由上诉四部分所组成，但对于开发者来说，gas_price * gas_limit 是一个已经习惯使用的方法，因此如果l2要求开发者换一种新方式进行gas计算，那么将加大开发者的开发难度。

因此，arbitrum中也通过了一系列方式实现了这种方式的计算，我们取`l2 compuational gas price`（也就是`arbgas_price`）作为该算式的gas_price部分，那么gas_usage的使用方法将变为（`L1 fixed cost` + `L1 calldata cost` + `L2 computational cost` + `L2 storage cost`）/`arbgas_price`，展开可得`l2 computaional gas usage` + （`L1 fixed cost` + `L1 calldata cost` + `L2 storage cost`）/`arbgas_price`, 因此我们可以看到，随着l1 gas的变化或者`arbgas_price`的变化，最终的gas usage会出现不相同的情况，这也是为什么在奥德赛期间l2的`gas usage`和以往相似交易的开销相差较大的原因。
