# 安装 Classroom Copilot 2.0 测试版

Classroom Copilot 是一个可移植的技能包。将整个 `classroom-copilot/` 文件夹复制到你的 Agent 所使用的技能目录，并保留其中的 `SKILL.md`、`references/` 与 `assets/` 内容。

## 安装位置

按所用平台选择一个位置：

| 可移植性层级 | 复制目标 |
| --- | --- |
| 通用／项目级 | `.agents/skills/classroom-copilot/` |
| Codex 用户级 | `~/.codex/skills/classroom-copilot/` |
| Pi 用户级 | `~/.pi/agent/skills/classroom-copilot/`，或 `~/.agents/skills/classroom-copilot/` |
| WorkBuddy 用户级 | `~/.workbuddy/skills/classroom-copilot/`，或通过 Skills UI 导入 |
| Reasonix | 使用 Skills 命令／UI 导入，或复制到其已配置的 skills 目录 |

项目级安装适合随课程项目共享；用户级安装适合在同一 Agent 的多个项目中复用。若平台同时发现多个位置，以该平台的技能发现规则为准。

这些是安装适配指引，不代表本测试版已在所有宿主完成实测。安装目录以宿主当前配置为准；保留整个文件夹，不只复制 SKILL.md。无需新 App 或后台服务。

## 能力要求与降级

Markdown 的读取与写入是必需能力：它用于保存可跨 Agent 续接的课堂会话。若无法进行持久写入，Copilot 会提供可复制的 Markdown 会话记录，但不能保证跨 Agent 续接。

以下能力均为可选；缺失时不应阻止基本的课堂记录与复习流程：

- 视觉理解（vision）：用于处理幻灯片、照片和截图。
- 搜索（search）：仅在学习者明确要求时用于补充外部来源。
- Mermaid 渲染：用于渲染概念图；仍可编辑 Mermaid Markdown。
- 图像生成（image generation）：用于生成视觉化素材。

## 2.0 使用与旧记录

可以说“设置学习目标：两周后能讲清专业Skill五层”“基于本节课开始自主回忆”“让我给初学者讲一遍”“恢复这个会话目录”。学习判断依据实际回答、提示使用与来源，不把 AI 生成成果算作掌握。

课堂会话保存在选定课程目录；课程父目录的 learning-profile.md 和 learning-ledger.md 跨节课保存目标与证据。不同学习者使用不同目录；跨 Agent 续接需带上整个课程目录，而非仅一节课。材料只取得摘录时，复盘只覆盖摘录。

旧版记录可以读取；必要状态写入前保留 _state.v1-backup.md，不改写原笔记或凭空补出学习证据。升级前建议自行备份课程目录。单纯恢复不会创建新来源或练习；多候选目录会询问选择。

当前为手动唤醒：可以保存约定复习日期，下次唤醒检查到期项，但关闭 Agent 后不会自行运行或推送。常驻硬件、调度器和第三方同步留待未来版本。

无文件能力仍可在聊天练习并复制临时记录，但不保证跨会话恢复。分享默认只生成新的本地副本，排除私密感悟、学习档案和练习原答；不自动上传或发布。

## 验证

安装或导入后，在对应 Agent 中输入：

> 启动课堂 Copilot，课程是测试课

若 Agent 进入 Classroom Copilot 流程并创建或恢复 Markdown 会话，即表示技能已被发现。若未被发现，确认复制的是整个 `classroom-copilot/` 文件夹、目标目录拼写正确，并按平台要求重启或刷新 Skills。
