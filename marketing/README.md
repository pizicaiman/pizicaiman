# 运营营销物料索引

本目录是基于 [Skills.md](../Skills.md)（中文）/ [Skills-en.md](../Skills-en.md)（英文）派生的运营营销物料体系。每件均为独立文件，便于单独发送给客户或在社交平台发布；均不含价格（线下沟通）。

## 板块总览

| 板块 | 目录 | 用途 | 入口 |
|------|------|------|------|
| 营销口号 | slogans/ | 朋友圈/官网/海报/方案封面传播 | [slogans/](slogans/) |
| 小合作单品 | deals/ | 轻量标准化服务，快速成交 | [deals/](deals/) |
| 朋友圈短文案 | moments/ | 把口号改写成真人朋友圈口吻 | [moments/README.md](moments/README.md) |
| 一页式提案模板 | proposals/ | 单品成交前的一页式提案 | [proposals/README.md](proposals/README.md) |
| 运维架构面经 | interview/ | 抖音/小红书日常内容沉淀 | [interview/README.md](interview/README.md) |
| 社媒自我宣传 | social/ | 抖音/小红书主页简介与口播 | [social/social-intro.md](social/social-intro.md) |

> 口号与单品文件内已分别补充"真实项目案例（脱敏）+ 市场分析"与"客户常问 3 问 FAQ"。

---

## 一、营销口号（slogans/）

| # | 主题 | 口号 | 文件 |
|---|------|------|------|
| 01 | 云原生底座 | 把集群交给标准，把精力交给业务。 | [01-cloud-native-base.md](slogans/01-cloud-native-base.md) |
| 02 | DevOps 流水线 | 让每一次提交，都安全地走向生产。 | [02-devops-pipeline.md](slogans/02-devops-pipeline.md) |
| 03 | K8s 运维优化 | 集群不是装上就稳，稳是治理出来的。 | [03-k8s-ops-tuning.md](slogans/03-k8s-ops-tuning.md) |
| 04 | 可观测性 | 在客户报障之前，先看见系统在喊疼。 | [04-observability.md](slogans/04-observability.md) |
| 05 | 数据库与中间件 | 数据不丢、不慢、不断。 | [05-db-middleware.md](slogans/05-db-middleware.md) |
| 06 | 项目管理与敏捷 | 让进度看得见，让风险说得清。 | [06-pm-agile.md](slogans/06-pm-agile.md) |
| 07 | 产品工程陪跑 | 从一个想法，到一项可运维的产品。 | [07-product-engineering.md](slogans/07-product-engineering.md) |
| 08 | 架构咨询 | 重大决策之前，先付一次便宜的学费。 | [08-architecture-consulting.md](slogans/08-architecture-consulting.md) |
| 09 | 运维托管 | 你聚焦业务，我守住底线。 | [09-ops-managed-service.md](slogans/09-ops-managed-service.md) |
| 10 | 端到端伙伴（总定位） | 不止交付服务，更做你数字化路上的长期伙伴。 | [10-end-to-end-partner.md](slogans/10-end-to-end-partner.md) |
| 11 | 安全与合规 | 安全不是加锁，而是基线。 | [11-security-compliance.md](slogans/11-security-compliance.md) |
| 12 | 自动化与效率 | 让重复的事交给机器，让人去做判断。 | [12-automation-efficiency.md](slogans/12-automation-efficiency.md) |

---

## 二、小合作单品（deals/）

| # | 单品 | 一句话价值 | 文件 |
|---|------|------------|------|
| 01 | Kubernetes 集群健康巡检 | 一次系统体检，把隐患挖在故障之前。 | [01-k8s-health-check.md](deals/01-k8s-health-check.md) |
| 02 | CI/CD 流水线搭建 | 打通"代码到生产"的自动化交付链路。 | [02-cicd-pipeline-setup.md](deals/02-cicd-pipeline-setup.md) |
| 03 | Prometheus + Grafana 监控接入 | 给系统装上"神经系统"，关键指标一屏可见。 | [03-prometheus-grafana-onboarding.md](deals/03-prometheus-grafana-onboarding.md) |
| 04 | 告警治理与收敛 | 让告警从"噪声"变回"信号"。 | [04-alert-noise-reduction.md](deals/04-alert-noise-reduction.md) |
| 05 | MySQL 慢查询优化专项 | 把"卡"在数据库的体验，从根上治掉。 | [05-mysql-slow-query-tuning.md](deals/05-mysql-slow-query-tuning.md) |
| 06 | Redis 容量与高可用评估 | 让缓存既撑得住流量，也扛得住故障。 | [06-redis-capacity-ha-assessment.md](deals/06-redis-capacity-ha-assessment.md) |
| 07 | Kafka 集群体检 | 让消息流不积压、不丢、不脑裂。 | [07-kafka-cluster-checkup.md](deals/07-kafka-cluster-checkup.md) |
| 08 | Docker 镜像规范与瘦身 | 更小的镜像、更快的发布、更少的攻击面。 | [08-docker-image-optimization.md](deals/08-docker-image-optimization.md) |
| 09 | ArgoCD GitOps 部署落地 | 把部署变成 Git 里的一次提交。 | [09-argocd-gitops-setup.md](deals/09-argocd-gitops-setup.md) |
| 10 | 集群安全基线检查 | 关上不该开的口子。 | [10-cluster-security-baseline.md](deals/10-cluster-security-baseline.md) |
| 11 | 灾备演练 | 别等真出事才知道恢复要多久。 | [11-disaster-recovery-drill.md](deals/11-disaster-recovery-drill.md) |
| 12 | 技术架构评审 | 重大决策之前，先付一次便宜的学费。 | [12-architecture-review-session.md](deals/12-architecture-review-session.md) |
| 13 | 敏捷流程导入工作坊 | 从"感觉能上线"变成"有据可上线"。 | [13-agile-onboarding-workshop.md](deals/13-agile-onboarding-workshop.md) |
| 14 | 上线就绪评审 | 把"差不多"变成"没问题"。 | [14-go-live-readiness-review.md](deals/14-go-live-readiness-review.md) |
| 15 | 故障复盘 facilitation | 把每一次故障，变成组织进化的燃料。 | [15-incident-retro-facilitation.md](deals/15-incident-retro-facilitation.md) |

---

## 三、口号 ↔ 单品 ↔ 方案 对应关系

每个单品文件末尾均标注了适配口号与方案，便于组合推介：

- **方案 A 云原生底座** → 口号 01 → 单品 01、10
- **方案 B DevOps 流水线** → 口号 02 / 12 → 单品 02、08、09
- **方案 C K8s 运维优化** → 口号 03 → 单品 01、11、14
- **方案 D 可观测性** → 口号 04 → 单品 03、04、15
- **方案 E 数据库与中间件治理** → 口号 05 → 单品 05、06、07
- **方案 F 项目管理敏捷** → 口号 06 → 单品 12、13、14
- **方案 G 产品工程陪跑** → 口号 07 / 10 → 单品 13、14
- **方案 H 架构咨询评审** → 口号 08 → 单品 12、14
- **方案 I 运维托管** → 口号 09 / 10 → 单品 01、11、15
- **横切：安全合规** → 口号 11 → 单品 10、14
- **横切：自动化效率** → 口号 12 → 单品 08、09

---

## 四、使用建议

- **首次触达**：发对应口号文件，一句话建立认知。
- **兴趣确认**：发对应小单品文件，明确交付边界，降低决策门槛。
- **深度合作**：发 Skills.md 中对应方案（A-I），进入正式合作。
- **组合推介**：一个口号 + 1-2 个小单品 + 对应方案，构成一条完整的"认知-试单-深度合作"路径。
