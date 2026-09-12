# 安装 Classroom Copilot

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

## 能力要求与降级

Markdown 的读取与写入是必需能力：它用于保存可跨 Agent 续接的课堂会话。若无法进行持久写入，Copilot 会提供可复制的 Markdown 会话记录，但不能保证跨 Agent 续接。

以下能力均为可选；缺失时不应阻止基本的课堂记录与复习流程：

- 视觉理解（vision）：用于处理幻灯片、照片和截图。
- 搜索（search）：仅在学习者明确要求时用于补充外部来源。
- Mermaid 渲染：用于渲染概念图；仍可编辑 Mermaid Markdown。
- 图像生成（image generation）：用于生成视觉化素材。

## 验证

安装或导入后，在对应 Agent 中输入：

> 启动课堂 Copilot，课程是测试课

若 Agent 进入 Classroom Copilot 流程并创建或恢复 Markdown 会话，即表示技能已被发现。若未被发现，确认复制的是整个 `classroom-copilot/` 文件夹、目标目录拼写正确，并按平台要求重启或刷新 Skills。
