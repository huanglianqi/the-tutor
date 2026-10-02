# 库内落盘与通道约定（the-tutor 唯一权威）

> **单一事实源。** the-tutor 怎么读写 Obsidian 学习库、走什么通道、写哪些文件，**只在本文件定义**。
> "面向 Obsidian 学习库" = 读写库里的 Markdown + frontmatter + wikilink，**仅此而已**，不是运维手册。

## 1. 通道：Obsidian MCP 首选，文件 I/O 退化

**开场先探测一次**：看手头的工具里有没有 Obsidian MCP 工具（`obsidian_list_vaults` / `obsidian_read_note` /
`obsidian_create_note` / `obsidian_edit_note` / `obsidian_search_vault` 等）。

- **有 MCP → 首选走 MCP**：
  - 用 `obsidian_list_vaults` 确认当前库的 vault id；
  - 读笔记用 `obsidian_read_note`（会返回 etag），改笔记用 `obsidian_edit_note` 带 `if_match`（etag 版本守卫，防并发覆盖）；
  - 新建用 `obsidian_create_note`，改名用 `obsidian_move_note`（自动更新 backlink）；
  - 破坏性操作用 trash 模式（可恢复，有日志）。
- **没有 MCP → 退到文件 I/O**，并且**明说一句**："本会话无 Obsidian MCP，用文件直接读写，没有 etag 并发守卫。"

> 为什么首选 MCP：etag 版本守卫把"防并发覆盖"这件事免费做了（库版当年要自己造 lock 文件）；
> backlink 自动一致（改笔记名不断链）；破坏性操作可恢复。这些都是直接文件读写拿不到的。

## 2. 学习库根解析（顺序，命中即停）

1. **工作区即学习库**：cwd 含 `.obsidian/`（或用户明说"就用这个库"）。
2. **学习者在本会话给出路径**。
3. 以上都失败 → **直接问**。不在磁盘上乱找，不另建新库。

> 解析成功后**不写任何记录文件**记住路径（那是另一个库版技能的做法：把绝对路径写进记录文件——跨设备必错）。
> 每次会话按上面顺序重新解析即可；有 MCP 时用 `obsidian_list_vaults` 拿 vault id。

## 3. 落盘物：三样核心 + 一样可选

```
<库>/<主题>/学习地图.md     导航层：依赖图 + 状态总览 + 「## 下次入口」，不重复知识内容
<库>/<主题>/<知识点>.md     每个闭环节点一篇（v1 就做）；frontmatter 带状态，wikilink 连成边
<库>/学习者画像.md          跨主题复用：floors(已会)/edges(边缘)/misconceptions(系统性错误认知)
```

- **边用 wikilink，状态用 frontmatter**：节点笔记里 `prereq: "[[前置节点]]"` 让 Obsidian 图谱**自动画出依赖图**，
  不用维护第二份图；`status` 字段让 Dataview 能查询"还剩哪些没掌握"。
- **学习地图是导航层**：只放依赖图总览、状态快照、「下次入口」——**不重复知识内容**（内容在节点笔记里）。
- **学习者画像是跨主题的**：摸底时更新 floors/edges/misconceptions，换主题也能复用（尤其系统性错误认知，最难查也最值钱）。
- **会话笔记**（每会话一篇实时记录）：v1 不强制，会话信息先浓缩进「下次入口」；发现不够再上。

## 4. 三件事必须落盘、且不能攒到最后

1. **地图**：阶段 0 建，阶段 1 剪，阶段 3 每闭环一次长一格。
2. **节点状态**：闭环通过 → 节点笔记 frontmatter 写 `已掌握`；摸底通过 → 写 `已确认`。
3. **下次入口**：收尾或中途挂起都必写，**没写 = 这一场没结束**。

> 状态字段取值只以 `state-model.md` 为准，本文件不重述。

## 5. 边界：只碰自己写的东西

- **只读/写自己那个 `<主题>/` 目录 + `学习者画像.md`**，不碰库中其他笔记——但**可以利用**它们：
  wikilink 到库内已有笔记、读它们的 frontmatter，是允许且鼓励的（这正是"面向库"的价值）。
- **不建锁、不做完整性检查、不扫描全库**。冲突风险由 MCP 的 etag 守卫（或 Obsidian 自身）承担。
- **导入别处已有记录，只当假设**：来源路径由学习者指定（不自己搜），每个"已掌握"仍要当场最小复现才认。
