# 本工作区给 DSH 的指令

这个目录是**面向 Obsidian 学习库的教学技能 `the-tutor` 的开发库**——不是知识库、不是 Obsidian 仓库、不是代码仓库（只是带 git 历史的技能开发目录）。

## 这里有什么

```
~/Projects/the-tutor/                      ← 本仓库：技能开发库（git 在这）
  AGENTS.md                                本文件（不随技能复制）
  NOTES.md                                 开发笔记 / 变更记录（不随技能复制）
  DESIGN.md                                架构设计稿（历史，不随技能复制）
  .agents/skills/the-tutor/                技能本体（整份复制即可带走）
    SKILL.md                               引导教学法全文（唯一规范源）
    references/state-model.md              状态字典【唯一权威】
    references/vault-spec.md               通道 + 库内落盘约定【唯一权威】
    references/learning-map.md             学习地图（怎么建、怎么长）
    references/diagnostic-framework.md     摸底诊断（L1/L2/L3 + 边缘夹逼）
    references/socratic-guidelines.md      引导细则（三种讲授方式、闭环）
    references/session-handoff.md          会话交接（记录边界 / 下次入口）
    scripts/                               （v1 空，反脚本膨胀）
  tools/check_the_tutor.py                 结构校验 + 自检（不随技能复制）
```

## 三条硬规矩（从两个旧技能的教训里立下的，别破）

1. **单一事实源。** 任何规则、判定表、状态字段、模板，**只允许有一份权威落点，别处只放指针**。
   状态字段唯一权威 = `references/state-model.md`；通道 + 落盘唯一权威 = `references/vault-spec.md`。
   别的文件引用它们的名字，**不得重述定义**。
2. **版本可观测。** frontmatter 会被加载器剥掉，模型问不到 `metadata.version`。所以正文顶部
   必须有一行 `> **技能版本 X.Y.Z**`，由 `check_the_tutor.py` 断言它与 `metadata.version` 一致。
3. **技能本体内不出现只在本机成立的路径/解释器别名/会漂移的数字快照。** 判定标准一句话：
   **别的项目的读者看到它，会不会被误导去做错一件事？** 那类内容写进本文件或 NOTES.md。

## 改完必跑

```bash
python3 tools/check_the_tutor.py              # 结构断言（FM/VERSION/REF/HEADING/SCRIPT-CAP/CITE）
python3 tools/check_the_tutor.py --self-test  # 改动本脚本后必跑（9 个当场造夹具的用例）
```

版本号约定：语义化 `X.Y.Z`。严重度按"旧东西是否还能通过"判：
- **MAJOR**：改变既有行为契约（删必填动作、改输出格式）；
- **MINOR**：新增能力不破坏旧行为（新增一节、一份 reference、一个字段）；
- **PATCH**：措辞、举例、澄清。

## frontmatter 的调用策略（这里写错过会整份技能消失）

- DSH 加载器只认两个规范键：`disable-model-invocation`（true 则模型调不动）、`user-invocable`（false 则用户点不了）。
- ⚠️ **旧式驼峰键 `disableModelInvocation` / `modelInvocable` / `userInvocable` 会让整份技能被静默丢弃**，不是忽略那个键。改完 frontmatter 务必确认技能还在。
- `description` 进模型目录（≤500 字符，超了末尾触发词先被砍）；`whenToUse` 进界面目录，不参与模型侧触发。
- 本技能 `disable-model-invocation: false`（模型可主动调用）。

## 通道：Obsidian MCP 首选

the-tutor 读写学习库时，**首选 Obsidian MCP**（etag 版本守卫、backlink 自动一致、破坏性操作可恢复），
无 MCP 时退到文件 I/O 并明说无并发守卫。细则在 `references/vault-spec.md` §1，**不要在正文别处重述**。

## 两个待办（v1 之后才做，别在 v1 里塞）

1. **费曼考核 / 独立判定者**（v2）：题目只能从「交付清单」出，判定前先核对"考的内容 ⊆ 清单"
   （这是 SKILL.md §1 原则 16 的范围对齐不变量，别破）。实现形态是 sub-agent 判定者，不是升 preset。
2. **间隔复习 / 到期队列**（v2）：依赖"状态可被脚本查询"，先把状态落稳再谈。

## 提交纪律

- 提交只暂存本次改动：`git add <具体路径>`，不要 `git add -A`。
- 本机路径、数字快照、设备专属配置只写 NOTES.md，不写技能本体。
