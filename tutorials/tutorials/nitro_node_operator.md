## 在Arbitrum One迁移到Nitro过程中，对节点操作者(Node Operator)的指南

_言简意赅： 当我们在8月31日进行Arbitrrum One向Nitro的迁移时，为节点操作者提供了一份快速的说明。_


在Arbitrum One迁移到Nitro后，Arbitrum经典节点(classic nodes)将只提供经典链(Nitro前)的状态查询；为了正确同步迁移之后的链，任何运行节点的人都需要运行一个Nitro节点。以下，我们列出了需要遵循的步骤和相应的时间/事件，以确保你能做好准备来运行一个Nitro节点。

### 在8月31日Nitro迁移之前或期间
1. 下载最新的Nitro节点版本v2.0.0
    - Docker Image链接, Docker Hub链接

    - 在链成功迁移到Nitro后，需要使用这个版本来进行同步

    - 9月1日，Arbitrum Goerli和Rinkeby也需要这个版本

2. 使用最新的版本启动和/或升级Nitro节点(上面有链接)。


    - 可以在迁移前或迁移中进行，不过Arbitrum One上的Nitro节点在迁移完成后才会提供RPC，并回应请求

    - 链ID将保持不变：42161

    - 你也可以参考我们的Nitro节点部署说明

### 在8月31日的迁移过程中
3. 给Nitro节点一个种子数据库(seed database)，用于初始的链上同步。


- 对于种子数据库，有两种选择

    - 选项1：我们将在迁移过程中进行链状态的导出(state export)，当完成这个后，我们将上传导出的副本作为种子数据库。我们会在我们的公共频道上发布，你可以关注一下我们的Arbitrum Devs Twitter


    - 选项2：个别运营商可以从他们自己的Arbitrum经典节点中导出状态，并将其作为Nitro节点的种子/genesis数据库


4) 等待链的恢复和节点逐渐开始回应请求。


    - 在状态导出完成后，我们将启动我们的序列器和rpc端点，这时整个生态系统的Nitro节点应该将正常运行
