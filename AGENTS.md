# 项目开发约定

与用户全局层的系统提示词配套使用。

## 环境

- 本机 macOS（Apple Silicon），shell 为 zsh，Homebrew 通常位于 `/opt/homebrew`；远程环境以实际情况为准。

## 修改原则

- 首次修改相关区域前查看涉及的目录与文件；运行或配置工具时查配置、锁文件和 scripts；Git 仓库检查已有修改。后续按需补读。
- 按实际配置沿用架构与工具链，保留用户已有修改；新建文件、依赖与重构限于当前需求。
- 依赖一律安装在项目级环境（虚拟环境 / node_modules），用户明确要求时才全局安装。
- 仅在用户明确要求时 commit、push 或撤销用户修改。

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