# 项目开发约定

与用户全局层的系统提示词配套使用。

## 环境

- 本机 macOS（Apple Silicon），shell 为 zsh，Homebrew 通常位于 `/opt/homebrew`；远程环境以实际情况为准。

## 修改原则

- 首次修改相关区域前查看涉及的目录与文件；运行或配置工具时查配置、锁文件和 scripts；Git 仓库检查已有修改。后续按需补读。
- 按实际配置沿用架构与工具链，保留用户已有修改；新建文件、依赖与重构限于当前需求。
- 依赖一律安装在项目级环境（虚拟环境 / node_modules），用户明确要求时才全局安装。

## Git 与远端仓库

- 开发或修改前用 `git rev-parse --show-toplevel` 确认 Git 管理范围，已有仓库、worktree 或父级仓库沿用现有历史与分支。确认项目独立且未受 Git 管理时，在项目根目录初始化；主分支按项目约定，未指定时用 `git init -b main`。
- 按实际项目结构维护 `.gitignore`，排除本地私有配置、依赖目录、构建产物和临时文件；保留已有有效规则。
- GitHub 的远端管理与核验用 `gh`，GitLab（含自建实例）用 `glab`；平台以已有 remote 或用户指定为准。暂存、提交、拉取和推送仍由 Git 执行。
- 需要远端操作时，分别检查对应主机的 CLI 登录与 SSH 认证；复用现有账号、密钥和配置。CLI 缺失或认证未完成时，继续可完成的本地工作，说明所需条件，不自行全局安装工具。
- Git 传输使用目标仓库的 SSH 克隆地址，保留其主机、命名空间及自定义端口。已有 remote 先核对目标；若为 HTTPS，验证 SSH 可用后只调整当前仓库的对应地址，保留其他远端与全局协议配置。
- 本地 Git 初始化无需再次确认；创建远端、commit、push 或撤销修改仅在用户明确要求时执行。已授权新建远端时，使用对应 CLI，明确目标主机、命名空间与仓库名，未指定可见性时默认 private；缺少目标信息只询问缺失项。
- 获准提交时，按文件或修改块暂存本任务改动，检查暂存差异，保留其他未提交内容。推送前核对目标分支与远端历史；强制推送或改写已有历史须另有明确要求。推送后用对应 CLI 核验远端分支与本地提交一致。
- SSH 失败时区分网络、主机信任与密钥认证问题；GitHub 的 22 端口受限可按[官方 SSH 443 方案](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)处理，核验主机指纹并说明配置影响。GitLab 按实例实际设置处理，不套用 GitHub 专用端点，也不静默改用 HTTPS。

## Python：uv 项目工作流

- 新项目默认用 uv：`uv init` → `uv add <包>` → `uv run <命令>`；用 `requires-python` / `.python-version` 管理解释器，纯脚本项目不添加 `[build-system]`。
- 已有项目按 `pyproject.toml`、锁文件与文档确认工具链。uv 项目需要复现环境时用 `uv sync`，运行与测试用 `uv run`；其他项目沿用原依赖格式。
- uv 项目增删依赖用 `uv add` / `uv remove`，由其维护配置与锁文件，不以 `uv venv` + `uv pip install` 替代项目工作流。

## Node.js

- 按 `packageManager`、锁文件与文档选包管理器，未指定时默认 pnpm；冲突时先查清，保留原锁文件体系。
- 运行前查 `package.json` scripts；已安装 CLI 用项目命令或对应包管理器执行。临时 CLI：pnpm 项目用 `pnpm dlx`，npm 项目用 `npx`，其他项目沿用原工具链。

## 新项目默认选型（仅用户未指定时）

- Node.js 用 TypeScript。
- 需要独立后端的 Web 应用前后端分离，后端 FastAPI；前端 React 或 Vue，搭配 TypeScript、Vite 和 Tailwind CSS；静态页面和纯前端项目按需简化。
- 按需启用检查工具：Python 优先 pytest、Ruff、mypy，前端优先 ESLint、Vitest；覆盖率、组件测试与 E2E 按风险添加。

## 验证与交付

- 按影响与风险选择已有检查，完成项目规定的必跑项。缺陷修复补能检出原缺陷的回归测试；纯文档检查内容、引用与差异即可。
- 修根因；类型忽略与规则豁免限于最小范围并说明原因。缺检查工具时说明限制，不擅自补装整套工具。
- 检查通过后，仅有新改动、失败或疑点时扩大或重复验证；修复本次引入的问题，其他发现单独报告。
- 交付前核对各项要求与最终 diff（非 Git 目录比较修改前后），报告改动、实际检查结果及未验证事项。
