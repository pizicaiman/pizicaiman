# 一页式提案索引

本目录是基于 [deals/](../deals/) 小合作单品派生的**一页式提案**：把单品的价值、交付、边界、配合要求浓缩为一页可发送给客户的提案，用于兴趣确认后、签约前的正式沟通。

所有提案均：中文、不含 emoji、不含具体价格（统一写"价格：另行沟通"）、篇幅控制在一页内。

---

## 一、模板用法

1. 复制 [00-template-one-page.md](00-template-one-page.md) 为新文件，命名建议 `NN-单品名-proposal.md`（编号与 deals/ 单品对齐）。
2. 将标题中的 `[单品名]` 替换为实际单品名称。
3. 逐处替换 `[占位：...]` 标注的内容，重点参考对应单品文件（deals/ 下同编号文件）的：
   - 交付内容 -> 提案"交付内容"
   - 交付方式 -> 提案"交付方式与节奏"
   - 交付边界（不包含） -> 提案"交付边界（不包含）"
4. "背景与痛点""目标""客户需配合""预期成果""下一步"按客户实际填写，体现针对性。
5. 价格统一保留"另行沟通"，不写入具体数字。
6. 保持一页篇幅：每节 2-5 行，交付内容与边界用分点，避免段落堆砌。

---

## 二、文件清单

| # | 提案 | 对应单品 | 文件 |
|---|------|----------|------|
| 00 | 一页式提案模板（空白占位版） | - | [00-template-one-page.md](00-template-one-page.md) |
| 01 | Kubernetes 集群健康巡检 | [01-k8s-health-check.md](../deals/01-k8s-health-check.md) | [01-k8s-health-check-proposal.md](01-k8s-health-check-proposal.md) |
| 02 | CI/CD 流水线搭建 | [02-cicd-pipeline-setup.md](../deals/02-cicd-pipeline-setup.md) | [02-cicd-pipeline-setup-proposal.md](02-cicd-pipeline-setup-proposal.md) |
| 12 | 技术架构评审 | [12-architecture-review-session.md](../deals/12-architecture-review-session.md) | [12-architecture-review-session-proposal.md](12-architecture-review-session-proposal.md) |

---

## 三、使用建议

- **发送时机**：客户已对单品有兴趣、需要一份正式但轻量的书面沟通时发送。
- **定制原则**：示例提案为脱敏通用版，发送前务必按客户实际改写"背景与痛点""客户需配合"等节，体现针对性。
- **组合推介**：一页式提案 + 对应 [slogans/](../slogans/) 口号 + [Skills.md](../../Skills.md) 中的对应方案，构成"认知 - 试单 - 深度合作"完整路径。
- **篇幅红线**：若内容超出单页，说明范围蔓延，应回归单品边界或升级为完整方案（A-I）。
