# 分级矩阵 · 小 / 中 / 大型项目该配多少规范

## 判据：三条 simultaneous

| 维度 | 小型 | 中型 | 大型 |
|---|---|---|---|
| 团队规模 | 1 人 / solo | 1-5 人 | 5+ 人 / 多团队 |
| 预期周期 | < 2 周 ~ 2 月 | 2 月 ~ 6 月 | 6 月+ / 长期演进 |
| 版本迭代 | 一次性，无版本 | 有 v1/v2，可预期 | 多版本并行 + 维护分支 |
| 合规要求 | 无 | 内部合规 | SOC2 / HIPAA / 审计留痕 |
| 代码库 | 单目录，<10 文件 | 多模块 | 多仓 / monorepo |

**取三条中最高的那一档。** 拿不准往小一级选。

---

## 各档配什么

### 小型（solo / 原型 / 自用工具）

目标：agent 进来 30 秒知道怎么跑、什么不能碰。

```
AGENTS.md        # <80 行：定位 + 命令 + 结构 + 禁区
CONTEXT.md       # 可选，仅当项目有自己独有术语时
```

不做：ADR、任务清单、质量门槛。写了也没人维护。

### 中型（正经产品 / 有迭代）← 最常见

目标：跨会话、跨版本不丢背景；决策有据可查。

```
AGENTS.md            # 完整版
CONTEXT.md           # 术语表
docs/adr/000X-*.md   # 关键决策
tasks/todo.md        # 唯一任务清单
tasks/plan.md        # 可选，需要拆阶段时
```

### 大型（团队 / 长期 / 合规）

目标：多人多 agent 并行不打架；质量可机械校验；审计有据。

```
中型全套 +
CONSTRAINTS.md       # 质量门槛带数字（覆盖率/性能/包体积阈值）
tasks/plan.md        # 必选
specs/               # 功能规格
子目录 AGENTS.md     # monorepo 各包自带，就近覆盖
```

---

## 业界框架对照（供选型，不必照搬）

| 框架 | 定位 | 流程 / 命令 | 适合 |
|---|---|---|---|
| **OpenSpec** | 增量规格（只写变化的部分） | propose → apply → archive | 存量代码改造、小团队、快 |
| **Spec Kit**（GitHub，MIT） | spec-driven 工具集，v1.0.0（2026-08-21），132k★ | `/speckit.constitution` → `specify` → `plan` → `tasks` → `implement` | 中型新项目、要一致性 |
| **BMAD** | 模拟 12+ 人敏捷团队（PM/架构/开发/QA 角色） | 多阶段多智能体 | 大型、复杂、强合规 |

三者都是**要安装的工具链**（Spec Kit 需 `uv tool install specify-cli`，会往仓库生成模板目录），
不是纯文档约定——这是它们与 AGENTS.md 最实际的差别。

**选择顺序**（业界共识）：

1. 存量代码改造 → OpenSpec
2. 新项目 + 小团队 → OpenSpec 或 Spec Kit
3. 新项目 + 团队扩张期 → Spec Kit
4. 企业级 / 强合规 / 多仓 → BMAD

### 三层不要混：标准 / 工具链 / 生成器

| 层 | 代表 | 回答什么 | 代价 |
|---|---|---|---|
| **格式标准** | `AGENTS.md` | agent 去哪读、读什么 | 零，写个 md 就行 |
| **工作流工具链** | Spec Kit / OpenSpec / BMAD | 按什么顺序产出 spec→plan→tasks→code | 要装 CLI、生成模板目录、团队要改习惯 |
| **生成器**（本技能） | `project-constitution` | 我这个项目该建哪几层、填什么事实 | 零，纯提示词 |

> **最容易搞混的一点（务必分清）**：用 AGENTS.md **不需要安装任何东西，更不需要 `specify-cli`**。
> AGENTS.md 是纯 Markdown——无 schema、无必填字段、无构建步骤，手写完放根目录即生效，25+ 工具直接读。
> `specify-cli` 是 **Spec Kit 工具链**的安装器，只有要跑 spec→plan→tasks→implement 强制流水线时才装。
> 单人 / 自用 / 小团队：**只写 AGENTS.md 就够了**，上 Spec Kit 反而多一套模板目录要维护。
> 反过来，别因为装了 Spec Kit 就不写 AGENTS.md——两者覆盖不同层，Spec Kit 官方也这么说。

AGENTS.md 现由 Linux 基金会 **Agentic AI Foundation（AAIF）** 托管（2025-12 成立，
与 MCP、goose 同为创始项目），**6 万+ 开源仓库采用**，25+ 工具原生支持
（Codex / Copilot / Cursor / Gemini CLI / Windsurf / Devin / Aider / Zed / goose）。

**唯一例外：Claude Code 不原生读 AGENTS.md**，需另建 `CLAUDE.md` 只写一行 import 指过去。

### 一个命名巧合（容易搞混）

Spec Kit 的第一条命令叫 `/speckit.constitution`，与本技能同名，**但两者不是一回事**：

- **Spec Kit 的 constitution** = 项目的治理原则与不可协商约束，是它流水线的**第 1 步门禁**
  （后续 specify / plan / tasks / implement 全都得服从它）。
- **本技能的 constitution** = **生成整套规范骨架的装配线**（决定建哪几层、按什么规模裁剪、
  怎么从实况考古出事实）。

真正该借鉴的是它的**门禁思想**：宪法没立，就不许往下拆解需求。
本技能把它落到 Step 5 验收——验收不过，不许进需求/任务阶段。

### 实务建议

**先写 AGENTS.md（30 分钟，立刻见效），有需要再上工具链。**
不要一上来就 BMAD——多数项目撑不起那个开销。

---

## 迁移路径（项目长大了怎么办）

规范是可以长出来的，不用一开始全建：

```
小型 AGENTS.md
   ↓ 开始有迭代、有多人
+ CONTEXT.md + docs/adr/ + tasks/todo.md   → 中型
   ↓ 有合规要求或团队扩张
+ CONSTRAINTS.md + specs/ + 子目录 AGENTS.md → 大型
```

反向也成立：项目进入维护期后，可以砍掉 plan/specs，保留 AGENTS.md + ADR。
**AGENTS.md 是唯一从头到尾都该存在的文件。**
