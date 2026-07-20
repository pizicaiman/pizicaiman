# K8s 架构原理 · 面经

## 核心问题
1. Kubernetes 的核心组件有哪些？各自职责是什么？
2. 一个 Pod 从提交到运行，完整流程是怎样的？组件之间如何协作？
3. etcd 在 K8s 中扮演什么角色？为什么不用 MySQL 这类数据库？

## 答题要点
- **控制面（Control Plane）组件**：
  - kube-apiserver：集群唯一入口，所有组件、kubectl、API 调用都经它访问 etcd，负责认证、鉴权、准入控制、乐观并发（ResourceVersion）。
  - kube-controller-manager：运行各类控制器的"协调循环"（reconciliation loop），如 Deployment 控制器维护副本数、Node 控制器监控节点健康、ReplicaSet/Endpoint 控制器维护端点。
  - kube-scheduler：根据资源请求、亲和性、污点容忍、优先级等约束，为未调度的 Pod 选择最优节点，并把 binding 写回 apiserver。
- **数据面（Data Plane）组件**：
  - kubelet：节点上的代理，向 apiserver 上报节点状态与 Pod 状态，调用容器运行时（containerd/CRI-O）启动、停止容器，执行健康检查与重启策略。
  - kube-proxy：维护 Service 到 Pod 的转发规则（iptables/ipvs/ebpf），实现集群内服务发现与负载均衡。
- **etcd**：强一致性分布式 KV 存储，存集群所有状态数据（Pod、Service、ConfigMap、Secret、集群配置）。基于 Raft 协议，建议 3 或 5 节点奇数部署，容忍少数派故障。
- **协作流程**：kubectl create -> apiserver 认证鉴权 + 写入 etcd -> scheduler watch 到新 Pod -> 过滤打分选节点 -> 写回 binding -> kubelet watch 到 -> 调用 CRI 启动容器 -> kubelet 上报 Running 状态 -> kube-proxy 更新转发规则。
- **控制器循环模式**：所有控制器都遵循"期望状态 vs 实际状态"的协调逻辑，最终一致，不保证实时一致。这是 K8s 声明式 API 的核心。

## 加分回答
K8s 的架构精髓在于"通过 apiserver + watch 机制实现组件解耦"。所有控制面组件都不直接互相调用，而是 watch apiserver 上的资源变化，各自做出反应。这种事件驱动模型让组件可以独立水平扩展——比如调度器压力大时可以独立扩容，而不影响 apiserver。但代价是引入了"最终一致"的延迟，生产中要理解这个特性，比如刚创建的 Service 到 Pod 转发规则生效可能有一两秒延迟。

etcd 是集群稳定性的命门。生产中 etcd 建议独立节点、独立 SSD、独立网络，避免和业务负载争抢资源。etcd 的性能直接决定 apiserver 的响应延迟，常见踩坑包括 etcd 磁盘满导致集群卡死、大对象（如超大 ConfigMap 或带 labels 的巨型 Secret）写入超时、etcd db size 超过 2GB 后性能雪崩。controller-manager 和 scheduler 在多副本部署时通过 leader election 保证只有一个实例在工作，避免脑裂。

## 口播版短文案
面试官问你 K8s 架构，别再背组件名了，给你一个能听懂的版本。K8s 就像一个公司，apiserver 是前台，所有人想改东西都得经过它；etcd 是档案柜，存所有状态，而且用 Raft 协议保证强一致；controller-manager 是项目经理，盯着目标不撒手，副本数少了就补；scheduler 是 HR，根据 Pod 的需求和节点的条件，给每个 Pod 找个最合适的工位；kubelet 是每个工位上的组长，负责把容器真正跑起来；kube-proxy 是路由器，让你通过 Service 名字就能访问到后面的 Pod。一个 Pod 从创建到运行，就是这五个角色接力把活干完。记住一句话：所有组件不直接说话，都通过 watch apiserver 来感知变化，这就是 K8s 声明式架构的灵魂。

## 标签
Kubernetes, 云原生, 运维面试, K8s架构, etcd, kubelet, 调度器
