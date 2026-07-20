# K8s 排障 · 面经

## 核心问题
1. Pod 一直 Pending 怎么排查？常见的几种原因是什么？
2. CrashLoopBackOff 反复重启怎么定位？OOMKilled 又是什么？
3. 节点 NotReady 怎么处理？会导致什么后果？

## 答题要点
- **Pod 状态速查表**：
  - Pending：调度失败或镜像拉取中。
  - ContainerCreating：卷挂载、网络配置、镜像拉取中。
  - Running：正常运行。
  - CrashLoopBackOff：容器反复启动失败，指数退避重试。
  - OOMKilled：内存超 limits 被 cgroup 杀死。
  - ImagePullBackOff：镜像拉取失败重试中。
- **Pending 排查**：`kubectl describe pod <name>` 看 Events。常见原因：资源不足（节点 allocatable 不够）、nodeSelector/nodeAffinity 无匹配节点、Taint 未被 Toleration 容忍、镜像仓库不可达、PVC 未绑定、调度器异常。重点看 Events 中 Scheduler 给出的理由。
- **CrashLoopBackOff 排查**：`kubectl logs <pod> --previous` 看上次崩溃前的日志（关键命令，不加 --previous 看的是当前已死的容器日志为空）。常见原因：应用启动失败（配置错、依赖连不上）、启动命令 command/args 错误、健康检查 livenessProbe 太激进被杀、镜像 entrypoint 异常退出。
- **OOMKilled**：看 `kubectl describe pod` 的 Last State，Reason 为 OOMKilled。区分是 limits 设太低还是应用内存泄漏。临时调大 limits，长期用 pprof/heap 剖析定位泄漏。注意 Java 应用要设 -XX:MaxRAMPercentage，别让 JVM 堆撑爆 cgroup。
- **网络不通分层定位**：
  - Pod 到 Pod：查 CNI 插件状态、节点路由、NetworkPolicy。
  - Pod 到 Service：查 Endpoints 是否为空（readiness probe 失败或 selector 不匹配）、kube-proxy 规则（iptables-save/ipvsadm -Ln）。
  - Pod 到外部：查 DNS 解析（CoreDNS）、节点 SNAT、网络策略出向规则。
- **节点 NotReady**：`kubectl describe node` 看 Conditions，Ready=False 或 Unknown。常见原因：kubelet 与 apiserver 失联（网络分区、apiserver 不可达）、PLEG 异常（Pod Lifecycle Event Generator，容器运行时卡死）、磁盘压力（DiskPressure）、内存压力（MemoryPressure）、容器运行时挂了。NotReady 后 Pod 仍运行但调度器不再分配新 Pod，超过 pod-eviction-timeout（默认 5 分钟）后开始驱逐。

## 加分回答
排障的"三板斧"要烂熟于心：`kubectl describe pod` 看 Events（调度、拉取、启动、探针、驱逐事件全在这）、`kubectl logs --previous` 看崩溃前日志（CrashLoopBackOff 的命门）、`kubectl get events --sort-by='.lastTimestamp'` 看集群级事件流。再进阶就是 `kubectl exec` 进容器抓现场、`crictl` 看容器运行时状态、`nsenter` 进容器网络命名空间抓包。生产建议把这套流程沉淀成 runbook，避免故障时手忙脚乱。

节点 NotReady 是大故障的前兆。最阴险的是 PLEG NotReady--kubelet 的 PodLifecycleEventGenerator 卡在等容器运行时返回状态，常见根因是 Docker/containerd hang 死、底层 containerd-shim 僵尸、节点负载过高 IO 卡顿。这时 Pod 不会立即被驱逐，但 kubelet 已经无法上报状态，5 分钟后 controller-manager 才会触发驱逐，期间业务可能已经受影响。处理顺序：先看节点负载与磁盘 IO，再 `systemctl status kubelet`、`crictl ps` 看运行时，最后才考虑重启 kubelet 或 containerd（重启会短暂影响该节点所有 Pod，谨慎操作）。严重情况下直接驱逐 Pod 后重启节点更稳妥。

## 口播版短文案
K8s 排障记住三板斧：describe 看 Events，logs --previous 看崩溃前日志，events 看集群事件流。Pod 一直 Pending，describe 一看 Scheduler 给的理由，八成是资源不够、nodeSelector 没匹配、污点没容忍、镜像拉不下来这几种。CrashLoopBackOff 反复重启，一定加 --previous 看上次崩溃的日志，不然你看到的是个空壳。常见就是配置错了、依赖连不上、健康检查太狠被杀。OOMKilled 看 describe 的 Last State，临时调 limits，长期查内存泄漏，Java 别忘了设 MaxRAMPercentage。节点 NotReady 最阴险的是 PLEG 异常，kubelet 卡在等容器运行时，先看磁盘 IO 和负载，别一上来就重启 kubelet。

## 标签
Kubernetes, 故障排查, CrashLoopBackOff, OOM, 节点NotReady, 运维面试, PLEG
