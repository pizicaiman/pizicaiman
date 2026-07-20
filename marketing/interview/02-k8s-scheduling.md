# K8s 调度与资源管理 · 面经

## 核心问题
1. Kubernetes 调度器如何为一个 Pod 选择节点？调度过程分哪几步？
2. requests 和 limits 有什么区别？不设置会怎样？QoS 等级怎么划分？
3. 如何让一个 Pod 只调度到特定节点？有哪些方式，各自适用场景？

## 答题要点
- **调度两阶段**：
  - 过滤（Filter/Predicate）：排除不满足硬性条件的节点，如资源不足、端口冲突、nodeSelector 不匹配、污点未容忍、亲和性规则不符。
  - 打分（Score/Priority）：对剩余节点按策略打分，如 LeastRequested（资源最空闲）、BalancedAllocation（CPU/内存均衡）、NodeAffinity/InterPodAffinity 权重、镜像本地化（已拉取镜像优先）。
- **资源模型**：
  - requests：调度依据，决定 Pod 能否被调度到某节点（节点 allocatable - 已 request ≥ Pod request）。
  - limits：运行时限制，CPU 超限会节流（CFS throttle），内存超限会触发 OOMKilled。
  - 都不设置 = BestEffort，节点压力大时最先被驱逐。
- **QoS 等级**（决定驱逐顺序与资源保障）：
  - Guaranteed：每个容器 requests = limits（CPU 和内存都设），最高优先级，最后被驱逐。
  - Burstable：requests < limits，至少有一个资源设了 requests。
  - BestEffort：都不设，最先被驱逐。
- **节点选择方式**（由弱到强）：
  - nodeSelector：简单标签等值匹配，已不推荐新用。
  - nodeAffinity：硬亲和（requiredDuringScheduling）+ 软亲和（preferredDuringScheduling），支持 In/NotIn/Exists/Gt/Lt 运算符。
  - Taint/Toleration：节点打污点排除 Pod，Pod 写容忍才能调度，常用于专用节点（GPU、磁盘节点）。
  - Pod Affinity/Anti-Affinity：根据已运行 Pod 的标签调度，用于就近部署（与缓存同节点）或打散（同一 Deployment 的副本分布到不同节点）。
- **调度扩展**：Scheduler Framework 插件化（1.19+），可在过滤、打分、绑定等扩展点自定义；优先级 PriorityClass 与抢占 Preemption 让高优 Pod 抢占低优 Pod 资源；DaemonSet 绕过调度器直接每节点一个。

## 加分回答
调度的难点不是"选一个节点"，而是"集群资源利用的长期健康"。默认调度器倾向于"打散"（AntiAffinity + LeastRequested），但会导致节点资源碎片化——每个节点都剩一点，大 Pod 调度不进去。生产中可以引入 binpack 策略（把 Pod 尽量堆到少数节点）提升密度，或用 descheduler 做二次重调度。另一个常见误区是"CPU request 设得很小，limit 设得很大"，结果 Pod 频繁被 CFS 节流，应用 RT 抖动却找不到原因。生产服务建议 CPU request/limit 相等走 Guaranteed QoS，既能避免节流，又能提升驱逐优先级。

资源管理还要关注"集群级"治理。LimitRange 给 Namespace 兜底默认值（不设 requests/limits 的容器自动注入默认值），ResourceQuota 限制 Namespace 的资源总量（CPU/内存/Pod 数/PVC 数），是租户隔离的基本手段。多租户场景下，配合 NetworkPolicy、PodSecurityPolicy/Admission Controller，才能形成完整的隔离闭环。

## 口播版短文案
面试被问 K8s 调度，记住这两步就够了：先过滤，再打分。过滤就是把不满足条件的节点踢掉，比如资源不够、节点标签不匹配、污点没容忍；打分就是在剩下的节点里挑最优的，谁空闲谁得分高。再讲 requests 和 limits，这是新人最容易踩的坑。requests 是调度依据，决定你 Pod 能不能上这个节点；limits 是运行时上限，CPU 超了会节流，内存超了直接 OOM 杀。重点来了，QoS 三个等级：Guaranteed 是 requests 等于 limits，最稳；Burstable 是中间档；BestEffort 是啥都不设，节点一紧张第一个被踢。生产服务的 CPU 建议两边设一样，不然 CFS 节流会让你排查 RT 抖动排查到怀疑人生。

## 标签
Kubernetes, 调度器, 资源管理, QoS, 亲和性, 运维面试, 云原生
