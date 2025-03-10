## 前置知识

### 服务注册与发现模型

#### 服务注册与发现过程
![[Pasted image 20241224202847.png|500]]
1. 服务端启动时，向注册中心注册自身信息；
2. 注册成功之后，服务端与注册中心保持心跳；
3. 客户端发起第一次请求之前，需要先获取注册中心所有可用服务的节点列表，并在本地缓存每个服务对应的节点列表；
4. 客户端与注册中心保持心跳和数据同步，服务端有变动则会通过注册中心通知客户端，客户端对应更新本地缓存的可用节点列表；
5. 客户端发送请求；
6. 服务器返回响应。

#### 服务下线过程
![[Pasted image 20241224203314.png|500]]
1. 服务端向注册中心发送下线通知；
2. 注册中心通知客户端某个服务端已下线；
3. 客户端收到通知，更新缓存，同时新进请求不会再发送给已下线服务端；
4. 服务端等待一段时间后，暂停服务并在线。

>**注意**：服务端下线是有延时的，因为需要通知注册中心与客户端自己已下线。

## 面试准备
注册中心回答要点：
1. 使用的注册中心中间件，以及对应优缺点；
2. 注册中心的集群规模；
3. 读写QPS；
4. 机器性能，如CPU与内存大小；
5. 注册中心故障后的排查与优化案例。、

### 基本模型
