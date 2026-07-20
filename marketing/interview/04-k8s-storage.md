# K8s 存储 · 面经

## 核心问题
1. PV、PVC、StorageClass 三者关系是什么？静态供给和动态供给有什么区别？
2. StatefulSet 的存储怎么处理？为什么有状态应用不能用 Deployment？
3. CSI 是什么？相比早期的 in-tree 插件有什么优势？

## 答题要点
- **三者关系**：
  - PV（PersistentVolume）：集群级存储资源，由管理员创建或 CSI 动态生成，独立于 Pod 生命周期。
  - PVC（PersistentVolumeClaim）：用户对存储的"申请单"，声明需要的容量与访问模式，与 PV 绑定后供 Pod 挂载。
  - StorageClass：描述存储"类型"与动态供给参数（provisioner、类型、 reclaimPolicy、绑定模式），实现按需创建 PV。
- **静态供给 vs 动态供给**：
  - 静态：管理员预创建 PV，PVC 按容量与访问模式匹配，匹配不上就 Pending。
  - 动态：PVC 引用 StorageClass，CSI provisioner 监听到 PVC 后自动创建底层卷并生成 PV 关联，按需即用。
- **访问模式（Access Modes）**：
  - RWO（ReadWriteOnce）：单节点读写，云盘、本地盘常见。
  - ROX（ReadOnlyMany）：多节点只读。
  - RWX（ReadWriteMany）：多节点读写，需 NFS/CephFS 等共享文件系统。
  - RWOP（ReadWriteOncePod）：1.22+，单 Pod 读写，更严格的隔离。
- **回收策略（Reclaim Policy）**：Retain（保留数据，需手动清理）、Delete（删除 PV 与底层卷）、Recycle（已废弃，不再推荐）。
- **CSI（Container Storage Interface）**：解耦 K8s 与存储驱动的标准接口。in-tree 插件耦合在 K8s 主仓库，发版慢、维护重；CSI 由厂商独立实现，支持动态扩容、快照、克隆、拓扑感知等高级能力，是 1.13+ 的标准方式。
- **StatefulSet 存储**：通过 volumeClaimTemplate 为每个 Pod 自动生成独立 PVC，Pod 与 PVC 一一对应且稳定（Pod 重建后挂回原卷），保证有状态应用的数据连续性。

## 加分回答
有状态应用为什么不能用 Deployment？根本原因是 Pod 名与存储的不稳定性。Deployment 的 Pod 名是随机后缀，重建后 PVC 重新绑定会丢失数据上下文；StatefulSet 的 Pod 名是稳定序号（如 mysql-0、mysql-1），PVC 也按序号稳定命名（通过 volumeClaimTemplate），Pod 重建后挂回原卷，数据连续。StatefulSet 还提供稳定的网络标识（headless Service + Pod DNS），是数据库、消息队列等有状态应用的标配。

存储选型是有状态应用成败的关键。生产建议：数据库、消息队列等对 IO 敏感的应用用本地盘或云盘（RWO），避免 NFS 的延迟与一致性风险；需要共享读写的场景（如多 Pod 共享配置、日志目录）才用 NFS/CephFS（RWX）。常见踩坑包括：用 NFS 跑 MySQL 导致写入抖动、用 RWX 卷跑单写多读导致锁冲突、PVC 容量满了扩容没开 AllowVolumeExpansion。高级能力上，CSI 快照用于备份恢复、拓扑感知（Topology）保证卷和 Pod 在同可用区、克隆用于环境复制，都是有状态应用运维的利器。

## 口播版短文案
K8s 存储三个概念，PV、PVC、StorageClass，很多人搞混。给你打个比方：PV 是仓库，PVC 是提货单，StorageClass 是仓库类型。静态供给就是管理员提前建好仓库等你来提，动态供给就是你下一张提货单，系统按你选的仓库类型自动给你盖一个。再讲有状态应用为啥必须用 StatefulSet 不能用 Deployment。Deployment 的 Pod 名是随机的，重建就变，数据找不回来；StatefulSet 的 Pod 名是稳定的 mysql-0、mysql-1，配 volumeClaimTemplate 给每个 Pod 单独建一个 PVC，Pod 重建还能挂回原来的盘，数据不断。最后一句话：数据库别用 NFS 跑，IO 抖到你想辞职，本地盘或云盘才稳。

## 标签
Kubernetes, 存储, PV, PVC, StorageClass, CSI, StatefulSet, 运维面试
