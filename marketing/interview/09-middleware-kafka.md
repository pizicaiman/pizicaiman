# 中间件 Kafka/Redis/Nacos · 面经

## 核心问题
1. Kafka 的分区、副本、ISR 是什么关系？acks=all 怎么保证数据不丢？Leader 选举怎么做的？
2. Kafka 消息积压怎么处理？分区数是不是越多越好？
3. Redis 持久化 RDB 和 AOF 怎么选？Nacos 集群怎么部署，为什么是双协议？

## 答题要点
- **Kafka 核心概念**：
  - 分区（Partition）：并行与吞吐单元，消息按 key 哈希路由到分区，单分区内有序。
  - 副本（Replica）：每个分区多副本，1 Leader + N Follower，Leader 负责读写，Follower 同步。
  - ISR（In-Sync Replicas）：与 Leader 数据差距在 `replica.lag.time.max.ms` 阈值内的副本集合，是"可信副本"。
  - acks=all（-1）：消息写入所有 ISR 副本才算成功，配合 `min.insync.replicas` 保证持久性，是生产推荐配置。
- **Leader 选举**：由 Controller（集群中一个 Broker）负责，从 ISR 中选第一个作为新 Leader。若 ISR 为空且开启 `unclean.leader.election.enable=true`，可能选非 ISR 副本导致数据丢失（默认 false 更安全但可用性降低）。
- **消息积压处理**：
  - 定位根因：消费者慢（处理逻辑重、外部依赖慢）、生产者突增、分区数不足。
  - 短期：扩消费者（数量 ≤ 分区数才有用）、临时下线非核心消费逻辑、扩容 Broker。
  - 长期：扩分区（注意 key 路由变化）、消费者异步批处理、消费者独立部署与资源隔离。
- **分区数并非越多越好**：过多导致 Leader 选举开销大、客户端连接数膨胀（每分区每消费者一个连接）、端到端延迟上升、Broker 元数据膨胀。经验值：单 Broker 分区数建议 <2000，集群总分区数 <20 万。
- **Redis 持久化**：
  - RDB：周期性快照，恢复快、文件小，但故障时丢最近一次快照后的数据。适合备份与容灾。
  - AOF：追加写命令，可配 `appendfsync`（always/everysec/no），everysec 折中安全与性能，丢失不超过 1 秒。
  - 生产推荐：RDB + AOF 混用（4.0+），AOF 保障安全，RDB 加速恢复与冷备。
- **Nacos 集群**：双协议设计--配置中心用 Raft（CP，强一致）、服务发现用 Distro（AP，高可用）。建议 ≥3 节点奇数部署，依赖 MySQL 持久化配置数据，服务发现数据各节点独立分片处理。

## 加分回答
Kafka 高吞吐的底层基础是四件套：顺序写磁盘（磁头不需寻道，顺序写速度堪比内存）、零拷贝（sendfile 系统调用，数据不经用户态）、PageCache（读写都走内核页缓存，消费者读热数据直接命中）、批量压缩（producer 端 batch + gzip/snappy/lz4，减少网络与存储开销）。理解了这四点，就明白为什么 Kafka 能做到单 Broker 数十万 QPS--它把磁盘当内存用，把网络当本地总线用。生产调优的核心也是围绕这四点：增大 `batch.size` 和 `linger.ms` 提升批量度、选择合适压缩算法、合理设置 `fetch.min.bytes` 让消费者也批量拉取。

Redis Cluster 与哨兵模式的选择是面试高频。哨兵（Sentinel）适合中小规模主从切换，单 master 写入，容量受单机限制；Cluster 用 16384 槽位分片，可水平扩展，但跨槽操作受限（mset、事务、Lua 跨槽报错），需用 hash tag（{tag}）让相关 key 落同槽。生产建议：数据量小（<32G）用哨兵简单可靠，数据量大或预期增长快用 Cluster 提前分片。Nacos 的双协议是亮点：服务发现用 AP 牺牲强一致换可用性（注册中心短暂不一致优于服务无法注册），配置中心用 CP 保证配置全局一致，这是阿里巴巴在电商高并发场景沉淀出的务实设计。

## 口播版短文案
Kafka 三个概念搞清楚就入门了：分区是并行单元，副本是高可用保障，ISR 是可信副本集合。acks=all 就是消息写入所有 ISR 才算成功，生产必配。Leader 挂了由 Controller 从 ISR 里选新的，ISR 空了开启 unclean 选举可能丢数据，默认关掉更安全。消息积压三步走：先看根因是消费者慢还是生产者突增，短期扩消费者但别超过分区数，长期扩分区或者批处理。分区不是越多越好，单 Broker 别超 2000，不然元数据膨胀选举开销大。Redis 持久化生产用 RDB 加 AOF 混用，AOF 保安全 RDB 加速恢复。Nacos 记住双协议：配置走 Raft 强一致，服务发现走 Distro 高可用，这是电商场景的务实设计。

## 标签
Kafka, Redis, Nacos, 消息积压, ISR, 中间件, 运维面试, 高可用
