# 运维架构面经索引

本目录是基于 [Skills.md](../../Skills.md) 派生的运维架构面经板块，用于抖音/小红书日常宣传。

每篇为一个独立面经，结构统一：核心问题、答题要点、加分回答、口播版短文案、标签。口播版适合 30-60 秒短视频拍摄，可直接念稿。

---

## 面经目录

| # | 主题 | 核心问题 | 文件 |
|---|------|----------|------|
| 01 | K8s 架构原理 | K8s 核心组件职责、Pod 创建协作流程、etcd 角色与选型 | [01-k8s-architecture.md](01-k8s-architecture.md) |
| 02 | K8s 调度与资源管理 | 调度两阶段、requests/limits 与 QoS、节点选择方式 | [02-k8s-scheduling.md](02-k8s-scheduling.md) |
| 03 | K8s 网络 | Service 类型与 ClusterIP 实现、kube-proxy 模式、CNI 选型 | [03-k8s-network.md](03-k8s-network.md) |
| 04 | K8s 存储 | PV/PVC/StorageClass 关系、静态与动态供给、StatefulSet 存储 | [04-k8s-storage.md](04-k8s-storage.md) |
| 05 | K8s 排障 | Pod Pending 排查、CrashLoopBackOff 定位、节点 NotReady 处理 | [05-k8s-troubleshooting.md](05-k8s-troubleshooting.md) |
| 06 | CI/CD 与 GitOps | 流水线阶段设计、GitOps 与传统 CI/CD 对比、灰度发布与回滚 | [06-devops-cicd.md](06-devops-cicd.md) |
| 07 | 可观测性与告警治理 | 指标/日志/链路三者关系、Prometheus 架构、告警收敛手段 | [07-observability.md](07-observability.md) |
| 08 | Linux 性能分析基础 | 性能分析工具分层、CPU/IO/网络定位、load average 含义 | [08-linux-essentials.md](08-linux-essentials.md) |
| 09 | 中间件 Kafka/Redis/Nacos | Kafka 分区/副本/ISR、消息积压、Redis 持久化、Nacos 双协议 | [09-middleware-kafka.md](09-middleware-kafka.md) |
| 10 | 场景设计：高可用与多活 | 高可用四要素、限流算法与分层、异地多活单元化、容量评估 | [10-scenario-design.md](10-scenario-design.md) |
| 11 | 中间件 RabbitMQ | 四种交换器、可靠性三道防线、死信/延迟队列、Quorum Queue | [middleware-rabbitmq.md](middleware-rabbitmq.md) |
| 12 | 中间件 Nacos | 双协议(Raft/Distro)、集群部署、服务注册发现、健康检查 | [middleware-nacos.md](middleware-nacos.md) |
| 13 | 中间件 Redis | 缓存穿透/击穿/雪崩、分布式锁与 Redisson 看门狗、Stream 队列 | [middleware-redis.md](middleware-redis.md) |
| 14 | 中间件 Logstash | input-filter-output 流水线、Grok 性能优化、PQ/DLQ | [middleware-logstash.md](middleware-logstash.md) |
| 15 | 中间件 Filebeat | harvester/spooler 协作、registry 与 at-least-once、选型组合 | [middleware-filebeat.md](middleware-filebeat.md) |
| 16 | 数据库 Elasticsearch | 倒排索引原理、分片/副本/路由、refresh/flush/merge | [dms-elasticsearch.md](dms-elasticsearch.md) |
| 17 | 数据库 MongoDB | WiredTiger 引擎、副本集/分片集群、分片键选择 | [dms-mongo.md](dms-mongo.md) |
| 18 | 数据库 MySQL | InnoDB B+树、MVCC/Next-Key Lock、主从复制与延迟优化 | [dms-mysql.md](dms-mysql.md) |
| 19 | 数据库 PostgreSQL | PG vs MySQL、MVCC 多版本元组、B-tree/GIN/GIST/BRIN 索引 | [dms-postgresql.md](dms-postgresql.md) |
| 20 | 数据库 Redis | 单线程模型、RDB/AOF 持久化、主从/哨兵/Cluster 集群 | [dms-redis.md](dms-redis.md) |

---

## 内容结构说明

每篇面经统一包含以下五个章节：

- **核心问题**：1-3 个该方向高频面试问题，用于引出主题。
- **答题要点**：分点作答，专业准确，3-6 点覆盖主要考点。
- **加分回答**：进阶视角与工程深度，体现候选人的实战经验与系统性思维。
- **口播版短文案**：30-60 秒短视频口播稿，口语化、有钩子，适合抖音/小红书。
- **标签**：5-8 个话题标签，用于平台分发与搜索。

---

## 使用建议

- **短视频拍摄**：直接取"口播版短文案"段落，配合核心问题作为标题与字幕。
- **图文笔记**：取"核心问题 + 答题要点"为主体，"加分回答"作为评论区置顶补充。
- **系列连载**：按 01-10 顺序发布，形成"云原生运维面试通关"系列，提升账号专业度与粉丝粘性。
- **直播选题**：每篇可扩展为 10-15 分钟直播切片，深度讲解"加分回答"中的进阶内容。
- **引流转化**：面经结尾可引导至 [Skills.md](../../Skills.md) 中的对应方案（A-I），承接技术咨询与合作。
