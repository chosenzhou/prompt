# prompt

维护两份给编码代理使用的提示词：系统提示词定义跨项目的协作方式，项目提示词定义具体项目的开发约定。以本仓库为源文件维护，再分发到工具实际读取的位置。

## 文件说明

| 文件 | 用途 | Codex 使用位置 |
| --- | --- | --- |
| [SYSTEM_PROMPT.md](SYSTEM_PROMPT.md) | 系统提示词：沟通、判断、任务授权与交付边界 | 默认复制到 `~/.codex/AGENTS.md` |
| [AGENTS.md](AGENTS.md) | 项目提示词：环境、工具链、修改原则与验证约定 | 目标项目根目录的 `AGENTS.md` |
| [README.md](README.md) | 使用与维护说明 | 供读者查阅 |
| [.gitignore](.gitignore) | 文件跟踪白名单 | 本仓库根目录 |

这里的“系统提示词”指用户全局层的职责，不代表 API 的 system 消息角色。`SYSTEM_PROMPT.md` 是源文件名；仅放在项目目录中，Codex 不会默认把它作为指令加载。全局与项目入口的区别见 [Codex 官方说明](https://learn.chatgpt.com/docs/agent-configuration/agents-md)。

## 使用方式

### 获取仓库

已安装并登录 GitHub CLI 时：

```bash
gh repo clone chosenzhou/prompt
cd prompt
```

本项目是 Markdown 文档仓库，无需安装 Python 或 Node.js 依赖。

### 配置系统提示词

在本仓库目录执行以下命令，会先备份已有文件，再用系统提示词覆盖全局入口。若已有需要保留的个人规则，应先比较并合并内容。

```bash
(
  set -e
  mkdir -p "$HOME/.codex"
  if [ -f "$HOME/.codex/AGENTS.md" ]; then
    cp -p "$HOME/.codex/AGENTS.md" "$HOME/.codex/AGENTS.md.backup-$(date +%Y%m%d-%H%M%S)"
  fi
  cp SYSTEM_PROMPT.md "$HOME/.codex/AGENTS.md"
)
```

若设置了 `CODEX_HOME`，使用对应目录替代默认的 `~/.codex`。全局目录中存在非空 `AGENTS.override.md` 时，它会优先于 `AGENTS.md` 被读取。[加载规则](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

### 配置项目提示词

将 `AGENTS.md` 复制到目标项目根目录。它与系统提示词配套使用；按目标项目实际环境调整开发约定，已有项目的配置与锁文件是判断工具链的依据。

在本仓库目录执行，将示例路径替换为已存在的目标项目绝对路径：

```bash
target_project="/path/to/project"
cp -n AGENTS.md "$target_project/AGENTS.md"
```

`cp -n` 保留已有同名文件，不做覆盖；目标项目已有 `AGENTS.md` 时，按差异合并，保留其专用约定。

当前分发版本已移除本工作区专用文档指引，可直接作为项目模板使用。若以后加入“本工作区文档”一节，复制到其他项目时移除该节。

### 确认生效

分发后在目标项目开启新的 Codex 会话，要求列出当前加载的全局与项目指令，核对实际内容。若没有加载预期文件，检查 `CODEX_HOME`、工作目录及同作用域的 `AGENTS.override.md`。[验证方法](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## 维护约定

- 跨项目的行为规则修改 `SYSTEM_PROMPT.md`，开发约定修改 `AGENTS.md`；同一规则保留一个权威出处。
- 修改源文件并提交后，按需同步到全局入口与目标项目。安装方式是复制，`git pull` 不会自动更新已分发的副本。
- 检查内容、相对链接和 `git diff --check`；重要行为调整在新的代理会话中验证，文本检查不等于模型行为实测。
- `.gitignore` 只允许上述四个文件被跟踪。`docs/`、`GLOSSARY.md`、`.scratch/` 及其他本地资料保留在本机，不随仓库分发。

提示词编写参考 Matt Pocock 的 [writing-for-agents](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md) 与 OpenAI 的 [GPT-6 提示指导](https://developers.openai.com/api/docs/guides/latest-model#prompting-best-practices)。使用这两份提示词无需额外安装该 skill。
