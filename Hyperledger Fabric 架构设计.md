## Hyperledger Fabric 架构设计

### 1.1 逻辑架构

Hyperledger Fabric 整体架构如下图

![](https://github.com/SHOU-EE-CS-GroupLabSummarySubmit/M200701407-Yaohaidong/blob/main/%E5%88%86%E5%B8%83%E5%BC%8F%E8%B4%A6%E6%9C%AC%E6%8A%80%E6%9C%AF%E6%9C%8D%E5%8A%A1.png?raw=true)

Hyperledger Fabric 四大核心组件：

1. 成员服务

2. 区块链服务

   > - 区块链（Blockchain）：区块之间以Hash连接为结构的交易日志。Peer 节点从 Order Service 节点接收交易区块，并根据背书策略和并发冲突标记区块上的交易是否有效，然后将该区块追加到 Peer 文件系统中的 Hash Chain 上。
   > - 交易（Transaction）：建议分为对链码的部署和调用两种操作类型。
   >   - 部署交易：部署是请求在 Peer 上启动链码容器；创建心得链码并设置一个程序作为参数。一个部署交易执行成功，表明链码已被成功安装到区块链上。
   >   - 调用交易：调用是从账本中请求读写集，是在之前已部署链码的情况下执行一个操作。调用交易将使用链码提供的一个函数。当成功时，链码执行特定的函数对账本数据进行操作（修改状态），并返回操作结果。

3. 链码服务

4. 事件：为各组件之间的异步通信提供技术实现。



### 1.2 运行时架构

![](https://github.com/SHOU-EE-CS-GroupLabSummarySubmit/M200701407-Yaohaidong/blob/main/%E5%AE%9E%E9%99%85%E8%BF%90%E8%A1%8C%E6%97%B6%E6%9E%B6%E6%9E%84%E5%9B%BE.png?raw=true)

 

### 1.3 交易处理流程

![](https://github.com/SHOU-EE-CS-GroupLabSummarySubmit/M200701407-Yaohaidong/blob/main/Hyperledger%20Fabric%20%E7%B3%BB%E7%BB%9F%E4%B8%AD%E4%BA%A4%E6%98%93%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B%E7%A4%BA%E6%84%8F%E5%9B%BE.png?raw=true)