# 项目连续性管理（project-continuity）

一个面向 Codex 的用户级全局 Skill，用于在长期项目中跨会话管理需求、资料、工作进度和交接状态。

本 Skill 采用显式调用策略：`policy.allow_implicit_invocation: false`。普通聊天不会自动触发项目文件修改。

## 功能模式

| 中文调用 | 内部模式 | 作用 |
| --- | --- | --- |
| `$project-continuity 初始化项目` | `init` | 检查并建立适合当前项目的持续管理档案 |
| `$project-continuity 同步进度` | `sync` | 将本次会话新增的重要事实增量写入项目文档 |
| `$project-continuity 交接会话` | `handoff` | 保存精简、可恢复的交接状态 |
| `$project-continuity 恢复项目` | `resume` | 在新会话中只读恢复当前项目状态 |

调用后可以附加关注范围，例如：

```text
$project-continuity 同步进度 重点整理本次产品库需求
```

只输入 `$project-continuity` 时，Skill 仅说明四种可用模式，不创建或修改文件。

## 推荐安装方式

### 让 Codex 根据仓库链接安装

在目标电脑的 Codex 中发送：

```text
请从以下 GitHub 仓库安装 project-continuity Skill：
https://github.com/lc1056867491/project-continuity-skill

安装为当前用户级全局 Skill，目标目录为：
~/.agents/skills/project-continuity/

安装前检查是否已存在同名 Skill；如存在，不要覆盖，先报告现状。
安装后验证 SKILL.md、agents/openai.yaml、四个 references 文件，以及
policy.allow_implicit_invocation: false。
```

### 使用 GitHub CLI

目标电脑已安装支持 Skill 的 GitHub CLI 时，可以运行：

```powershell
gh skill install lc1056867491/project-continuity-skill project-continuity --agent codex --scope user
```

`--scope user` 表示安装到当前用户的全局 Skill 目录，对该用户的所有 Codex 项目可用。

### 手动安装

将仓库中的整个 `skills/project-continuity/` 目录复制到：

```text
~/.agents/skills/project-continuity/
```

Windows 通常对应：

```text
C:\Users\<用户名>\.agents\skills\project-continuity\
```

安装完成后开启新的 Codex 会话，再显式调用本 Skill。

## 仓库结构

```text
skills/
└── project-continuity/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        ├── init.md
        ├── sync.md
        ├── handoff.md
        └── resume.md
```

## 设计原则

- 通用、跨项目复用，不写死任何行业信息。
- 每个项目的数据保存在该项目自己的工作目录中。
- 优先复用已有管理文档，不重复创建同类文件。
- 严格区分已确认、待确认、AI 建议、已完成和计划中。
- 同步和交接采用增量更新，减少重复内容和上下文成本。
- 恢复模式默认只读。
- 不读取或记录 API 密钥、令牌和其他凭证。
- 不自动修改 API、模型、环境变量或上下文压缩配置。

## 重要提醒

运行初始化、同步或交接模式前，请确认 Codex 当前工作目录确实属于目标项目。Skill 不会自动拥有旧会话的完整聊天记录，恢复能力取决于项目中已经保存的管理文档。
