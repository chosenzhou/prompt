# prompt

按工作场景维护给编码代理使用的提示词。系统提示词提供共用的协作规则，场景模板提供编程、写作、研究等工作各自需要的项目约定；以本仓库为源文件维护，再按需分发。

## 文件说明

| 文件 | 用途 | Codex 使用位置 |
| --- | --- | --- |
| [SYSTEM_PROMPT.md](SYSTEM_PROMPT.md) | 系统提示词：沟通、判断、任务授权与交付边界 | 默认复制到 `~/.codex/AGENTS.md` |
| [prompts/](prompts/) | 按工作场景组织的项目提示词模板 | 选定模板复制到目标项目根目录，命名为 `AGENTS.md` |
| [AGENTS.md](AGENTS.md) | 维护本提示词库的约定 | 仅在维护本仓库时使用 |
| [README.md](README.md) | 使用与维护说明 | 供读者查阅 |
| [.gitignore](.gitignore) | 文件跟踪白名单 | 本仓库根目录 |

这里的“系统提示词”指用户全局层的职责，不代表 API 的 system 消息角色。`SYSTEM_PROMPT.md` 是源文件名；仅放在项目目录中，Codex 不会默认把它作为指令加载。全局与项目入口的区别见 [Codex 官方说明](https://learn.chatgpt.com/docs/agent-configuration/agents-md)。

## 场景模板

| 场景 | 源文件 | 适用内容 |
| --- | --- | --- |
| 编程 | [prompts/coding.md](prompts/coding.md) | 开发环境、Git / SSH 与远端流程、Python / Node.js 工具链、修改原则与验证交付 |

写作、研究等模板在实际需要时添加到 `prompts/`。每个目标项目选择适用的模板；模板目录用于保存和选择内容，避免把所有场景规则一起加载。

## 使用方式

### 获取仓库

已安装并登录 GitHub CLI，且已配置 GitHub SSH 认证时：

```bash
gh repo clone git@github.com:chosenzhou/prompt.git
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

### 配置项目提示词：以编程场景为例

将 `prompts/coding.md` 复制到目标项目根目录，命名为 `AGENTS.md`。它与系统提示词配套使用；按目标项目实际环境调整开发约定，已有项目的配置与锁文件是判断工具链的依据。

在本仓库目录执行，将示例路径替换为已存在的目标项目绝对路径：

```bash
target_project="/path/to/project"
cp -n prompts/coding.md "$target_project/AGENTS.md"
```

`cp -n` 保留已有同名文件，不做覆盖；目标项目已有 `AGENTS.md` 时，按差异合并，保留其专用约定。

根目录的 `AGENTS.md` 负责维护这个提示词库；分发时使用 `prompts/` 中选定的场景模板。

### 编程模板的 Git 行为

模板在首次修改前确认 Git 管理范围，复用已有仓库、worktree 或父级仓库；独立项目尚无仓库时自动初始化，未指定主分支时使用 `main`。需要远端操作时，GitHub 使用 `gh`、GitLab 使用 `glab` 管理和核验远端，Git 传输采用 SSH。

本地初始化不自动触发远端创建、commit 或 push；这些动作按用户明确要求执行。完整的认证、远端目标和提交边界以 [prompts/coding.md](prompts/coding.md) 为准。

### 确认生效

分发后在目标项目开启新的 Codex 会话，要求列出当前加载的全局与项目指令，核对实际内容。若没有加载预期文件，检查 `CODEX_HOME`、工作目录及同作用域的 `AGENTS.override.md`。[验证方法](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## 维护约定

- 跨场景的行为规则修改 `SYSTEM_PROMPT.md`，场景要求修改 `prompts/` 中对应模板，提示词库的维护要求修改根目录 `AGENTS.md`；同一规则保留一个权威出处。
- 修改源文件并提交后，按需同步到全局入口与目标项目。安装方式是复制，`git pull` 不会自动更新已分发的副本。
- 检查内容、相对链接和 `git diff --check`；重要行为调整在新的代理会话中验证，文本检查不等于模型行为实测。
- 精简模板时对照原文核对已确认的约定、适用条件和授权边界；文件大小变化只作为维护指标。
- `.gitignore` 允许跟踪根目录四个入口文件及 `prompts/`，忽略 `.DS_Store`。`docs/`、`.scratch/` 及其他本地资料保留在本机，不随仓库分发。

新增场景时，在 `prompts/` 创建一个小写英文名称的 Markdown 文件，多词用连字符，如 `long-form-writing.md`；注明适用范围，补充该场景规则，再更新上方模板索引。无需为未来场景预建空文件。

提示词编写参考 Matt Pocock 的 [writing-for-agents](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md) 与 OpenAI 的 [GPT-6 提示指导](https://developers.openai.com/api/docs/guides/latest-model#prompting-best-practices)。使用这两份提示词无需额外安装该 skill。
