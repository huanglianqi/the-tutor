# 开发笔记 / 变更记录（不属于技能本体）

> 这个文件不随技能复制。它记的是**为什么这么做**、以及踩过的坑，让后人（包括未来的我）知道哪些决定不能随便改。

## 0.1.0 → 0.2.0（2026-10-02，MINOR）

从 staging 迁到正式家目录 `~/Projects/the-tutor/`，并按学习者拍板的 5 条决策落地：

1. **通道 = Obsidian MCP 首选 + 文件 I/O 退化**（`vault-spec.md` §1 重写）。理由：etag 版本守卫把
   "防并发覆盖"免费做了（库版当年靠 lock 文件 + TTL 30min 硬扛）；backlink 自动一致；破坏性操作可恢复。
   代价：学习者 DSH 必须配了 obsidian MCP；缓解 = 开场探测，无 MCP 退文件 I/O 并明说。
2. **记录约定升级**：从"一主题一目录 + 学习地图"升级为"地图（导航层）+ 每闭环节点一篇（frontmatter 的
   `status`/`prereq`，wikilink 成边）+ 学习者画像（floors/edges/misconceptions）"。核心原则：
   **边用 wikilink，状态用 frontmatter**，地图不重复知识内容。推翻了我自己在 0.1.0 里"节点笔记后置"的保守决定。
3. **范围对齐不变量**进 SKILL.md §1 原则 16。这是学习者点出的真问题：旧版费曼考核"考超范围导致反复失败"，
   根因是"考核范围 > 引导范围"（库版自己的 SKILL.md 也记着"考核考了没教的东西"两次翻车）。修法三件套：
   考核内容 ⊆ 交付清单；题目只从清单出 + 判定前核对；考核粒度 = 引导粒度（每节点闭环，天然对齐）。
4. **形态 = 单 skill（非 preset）**。preset 是"顶层 agent 组装"，skill 是"可复用组件"；the-tutor 要的是教学法，
   模块化靠 reference + 将来 sub-agent 判定者，不升 preset。
5. **家目录 = `~/Projects/the-tutor/`**。

## 0.1.0（2026-10-02，初始）

从零搭骨架（staging）：SKILL.md + 6 份 reference + `check_the_tutor.py`（8 类断言 + 自检）。

### 设计时的三个关键取舍（别回退）

- **三个不可切走的闭环不变量**：无提示复现闭环、会生长的学习地图、会话边界=记录边界+下次入口。
  这三样是"想法不依赖库"，用真实事故换来的。**判定者可以独立出去，闭环的环节不能。**
- **反负债清单**：SKILL.md ≤250 行、状态字段一处定义、scripts ≤1、零本机痕迹、description ≤500、
  版本可观测。全部由 `check_the_tutor.py` 机械把关。
- **明确不做**：费曼考核、间隔复习、锁/完整性检查、多设备同步、书目脚本——每一条都写了"为什么不做"。

### 借鉴来源（想法，非照搬）

- `socratic-tutor`（通用分叉）：闭环不可省、学习地图会生长、单一事实源、读真值不用代理指标。
- `socratic-feynman-tutor`（库版）：状态落盘、判定者独立、单一事实源分层——但它的 16 脚本/15 字段 frontmatter 是反面教材。
- Mimir（`tommyhedgerow/Mimir`）：两个教学原则（Unconditional truths first / "How could I have discovered this?"）、
  模块化、图不进上下文只传名字。注意：用户最初给的 `mimir-tutor-20d96e` 是**过时预设**，当前是 `mimir-tutor-simplified-chinese-51a04d`。

## 环境备忘

- 校验脚本 `check_the_tutor.py` 用极简 frontmatter 解析器，**不依赖 PyYAML**（Homebrew python3 没有它，
  "跑不起来=不存在"的检查器是最该避免的）。DSH 自带的 `node_modules/yaml` 可做真实 YAML 交叉验证。
- 本仓库的 git 只在本机操作；别在别的设备上跑 git status（会刷新 .git/index 造成 churn）。
