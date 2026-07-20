# K8s 网络 · 面经

## 核心问题
1. Kubernetes 中 Service 的几种类型有什么区别？ClusterIP 这个虚拟 IP 是怎么实现的？
2. kube-proxy 的 iptables 和 ipvs 模式有什么区别？大集群应该用哪个？
3. CNI 是做什么的？Flannel、Calico、Cilium 怎么选？

## 答题要点
- **Service 类型**：
  - ClusterIP：集群内虚拟 IP，通过 kube-proxy 规则负载到 Endpoints，默认类型。
  - NodePort：在每个节点开一个端口（30000-32767），外部可通过 节点IP:端口 访问。
  - LoadBalancer：依赖云厂商 LB，自动创建外部负载均衡器并指向 NodePort。
  - ExternalName：DNS CNAME 到外部域名，不生成转发规则。
  - Headless Service（clusterIP: None）：不分配虚拟 IP，DNS 直接返回 Pod IP，用于 StatefulSet。
- **ClusterIP 实现原理**：虚拟 IP，ping 不通，靠 kube-proxy 在每个节点维护 iptables/ipvs 规则拦截流量，DNAT 到后端 Pod IP。Endpoints/EndpointSlice 维护 Service 到 Pod 的映射，EndpointSlice 解决大规模 Service 下 Endpoints 资源过大问题。
- **kube-proxy 模式**：
  - iptables：规则链匹配，O(n) 复杂度，规则数随 Service×Pod 线性增长，大集群规则爆炸。
  - ipvs：内核 LVS，哈希查找 O(1)，支持 rr/wlc/sh 等多种调度算法，性能稳定，1.0+ 集群首选。
  - ebpf（Cilium）：绕过 iptables/netfilter，在 socket 层直接转发，性能最高，但需内核 4.19+。
- **Ingress**：七层路由（HTTP/HTTPS），由 Ingress Controller（Nginx/Traefik/Contour/APISIX）实现，根据 host/path 转发到 Service。Ingress 资源是规则，Controller 才是执行者。
- **CNI（Container Network Interface）**：负责 Pod 网络分配与跨节点通信。
  - Flannel：简单 Overlay（VXLAN）或 Host-Gateway，无网络策略，适合入门。
  - Calico：BGP 路由（三层），原生支持 NetworkPolicy，性能好，适合生产。
  - Cilium：基于 eBPF，高性能、可观测性强、支持 L7 策略，云原生新趋势。
- **NetworkPolicy**：基于标签的流量白名单（入向/出向），需 CNI 支持（Calico/Cilium 支持，Flannel 默认不支持）。用于租户隔离与零信任。

## 加分回答
K8s 网络的精髓是"同 Pod 共享网络命名空间，Pod 间通过 CNI 跨节点通信，Service 通过 kube-proxy 做负载均衡"。理解这套机制后，很多"玄学问题"都能定位：Pod 间不通查 CNI 与 NetworkPolicy；Service 不通查 kube-proxy 规则与 Endpoints；外部访问不通查 Ingress Controller 与 LoadBalancer。一个高频踩坑是 Service 有 ClusterIP 但 Endpoints 为空——通常是 Pod 没 ready（readiness probe 失败）或 label selector 写错，DNS 能解析但连不上。

大集群网络选型是面试加分项。千节点以下 iptables 还能撑，万节点以上必须上 ipvs 或 Cilium。Cilium 用 eBPF 替代 kube-proxy，能做 L7 流量治理、可视化、网络策略，是新一代网络方案的趋势。但 eBPF 对内核版本要求高，老旧内核（3.x）跑不了。生产选型还要考虑网络模型：Overlay（VXLAN/IP-in-IP）跨子网方便但性能损耗 5-10%；BGP/Host-Gateway 性能好但要求二层网络可达。多云/混合云场景下，Calico 的 BGP + route reflector 是常见方案。

## 口播版短文案
K8s 网络这块，新人最懵的就是 Service 到底是个啥。给你一句话：Service 是个虚拟 IP，ping 不通，靠 kube-proxy 在每个节点写规则把流量转发到后面的 Pod。四种类型记好：ClusterIP 集群内用，NodePort 开节点端口，LoadBalancer 云厂商给个 LB，ExternalName 就是个 DNS 跳转。再讲 kube-proxy 两种模式：iptables 是规则链一条条匹配，大集群规则爆炸；ipvs 是内核哈希查找，又快又稳，生产首选。CNI 选型三句话：Flannel 简单没策略适合学习，Calico 用 BGP 性能好支持网络策略适合生产，Cilium 用 eBPF 性能拉满但吃内核版本。面试官问大集群选哪个，直接答 ipvs 或 Cilium，稳。

## 标签
Kubernetes, 网络, Service, kube-proxy, CNI, Calico, Cilium, 运维面试
