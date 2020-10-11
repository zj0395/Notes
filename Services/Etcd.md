# Etcd
高可用的分布式键-值数据库
(https://github.com/etcd-io/etcd)
- *简单*: 定义良好的接口
- *安全*: 支持TLS传输协议
- *快速*: 10000写/s
- *可靠*: Raft一致性算法

典型场景：
- 服务发现
- 配置管理

## 概念
- Raft: 分布式一致性算法，是etcd的核心层，接收和处理各种消息，包括线性一致性读取的消息
- WAL: Write Ahead Log, 预写式日志，用户持久化

## Raft
(https://draveness.me/etcd-introduction)
### Leader选举
- 初始节点都为Follower，定时器超时后向其它节点发送成为Leader的请求，其它节点同意或拒绝
- Leader定时向其它节点发送心跳，确保自己仍为Leader
- Follower收到心跳请求将计时器清空

## 数据读写
### 写入
- 为保证强一致性，etcd集群中数据流向都是由Leader流向Follower，不一致的数据会被覆盖
- 写入Leader时，直接写入，Leader分发给Follower；向Follower写入也会由Leader分发给Follower
- 即Leader数据始终最新，Follower上不一定最新
### 线性一致性读取
- Leader通过心跳来保证自己是Leader
- 从Follower读取时，必须向Leader请求最新的Commit Index，和本节点上Commit Index比较，如果本节点非最新则更新后再返回读取结果，以避免读到了未更新的数据

## 持久化
- 使用(https://github.com/etcd-io/bbolt)[bblot]用于存储
- 底层每个key都有多个`generation`和`revision`，key被删除时`generation`结束，再次创建时新增一个`generation`
- 使用一个内存BTree来存储key的当前`revision`，再根据revision去查找数据
- 数据量大时创建快照

