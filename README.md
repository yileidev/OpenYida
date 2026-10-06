# OpenYida

> AI 原生的命令行工具，让你用自然语言对话驱动构建钉钉宜搭低代码应用

## 🎯 项目简介

**OpenYida** 是 AI 编码助手与宜搭平台之间的桥梁。通过自然语言对话，驱动快速完成应用开发，AI 打草稿、人工精修。

### 核心优势

- **AI 打草稿，人工精修** — AI 生成快速原型，开发者保留完全控制权
- **资产完全归属企业账号** — 享受企业级权限、审计和安全保障
- **模型自由选择** — 支持 Claude、GPT 等任意 AI 模型，不被平台绑定

### 工作流程

```
用户输入（自然语言）
    ↓
AI 智能体解析意图
    ↓
CLI 调用宜搭 API
    ↓
生成可编辑的宜搭应用
```

---

## 📖 目录

- [🚀 快速开始](#快速开始)
- [📋 功能全景](#功能全景)
- [⚙️ 安装与设置](#安装与设置)
- [📚 命令参考](#命令参考)
- [🤖 AI 工具集成](#ai-工具集成)
- [🏗️ 项目结构](#项目结构)
- [❓ 常见问题](#常见问题)

---

## 🚀 快速开始

### 前置要求

- Node.js ≥ 18
- npm 或 yarn
- 有效的钉钉宜搭企业账号

### 1 分钟安装

```bash
npm install -g openyida
```

### 2 检查环境

```bash
openyida env
```

### 3 登录宜搭

```bash
openyida login
```

### 4 验证成功

```bash
openyida app-list
```

完成！你现在可以和 AI 对话，如：

```
@宜搭 列出我当前可用的应用
```

---

## 📋 功能全景

| 功能领域 | 具体能力 |
|---------|---------|
| 应用管理 | 创建、更新、导入、导出宜搭应用 |
| 表单建模 | 创建表单、更新字段、获取 Schema、管理权限 |
| 自定义页面 | 生成 React 页面、代码检查、编译、发布 |
| 流程自动化 | 创建流程表单、配置审批流、预览流程实例 |
| 数据操作 | 查询表单/流程/任务/子表单数据、异常检查 |
| 集成管理 | HTTP 连接器、认证账号、自动化流管理 |
| 运维诊断 | 环境检测、登录态管理、CDN 资源上传 |

---

## ⚙️ 安装与设置

### 环境要求

| 依赖项 | 要求 |
|-------|------|
| Node.js | ≥ 18 |
| 包管理器 | npm 或 yarn |
| 宜搭账号 | 有效的开发者权限（企业开通） |
| 网络 | 能访问宜搭服务 |

### 全局安装

```bash
npm install -g openyida
```

安装完成后，会同时暴露 `openyida` 和 `yida` 两个命令。

### 环境检测

```bash
openyida env                # 基础检测
openyida env --json         # JSON 格式输出
openyida commands --json    # 输出命令清单（供 AI 读取）
```

### 登录认证

```bash
openyida login
```

**登录流程**（自动优先级）：
1. 优先尝试通过本地 Chrome/Edge CDP 自动登录
2. 若不可用，降级为 AI 对话二维码传递
3. 最终备选：终端二维码登录

**其他登录方式**：
```bash
openyida login --qr                    # 终端二维码登录
openyida login --qr --corp-id dingxxx  # 指定企业
openyida login --check-only --json     # 仅检查登录状态
```

**登录成功返回**：
```json
{
  "ok": true,
  "base_url": "https://boyo.aliwork.com",
  "corp_id": "ding8ecb6241a2974bb935c2f4657eb6378f",
  "user_id": "17810549415134980",
  "csrf_token": "78e5b3ae-f511-44...",
  "cookies_count": 37
}
```

Cookie 保存位置：`C:\WINDOWS\system32\.cache\cookies-public.json`

### 故障排查

#### Playwright chromium 缺失（Windows）

```powershell
# 以管理员身份运行 PowerShell
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\ms-playwright\chromium-1217"
mkdir -Force "$env:LOCALAPPDATA\ms-playwright\chromium-1217"
cmd /c mklink /D "%LOCALAPPDATA%\ms-playwright\chromium-1217\chrome-win64" "C:\Program Files\Google\Chrome\Application"
openyida login
```

#### 其他问题

| 问题 | 解决方案 |
|-----|---------|
| Node.js 版本过低 | 升级到 18+ |
| 登录二维码不显示 | 使用 `--qr` 参数 |
| 日期字段提交失败 | 使用 13 位毫秒时间戳 |

---

## 📚 命令参考

### 应用管理

```bash
openyida create-app "CRM"
openyida create-app --name "CRM" --desc "客户管理" --theme deepBlue
openyida app-list --size 20
openyida corp-efficiency
```

### 表单管理

```bash
openyida create-form APP_XXX "客户表单" .cache/openyida/forms/customer-fields.json
openyida create-form update APP_XXX FORM_XXX .cache/openyida/forms/customer-changes.json
openyida get-schema APP_XXX FORM_XXX
openyida get-schema APP_XXX --all --output-dir .cache/schemas
```

### 自定义页面开发

**内置模板**：`product-homepage`、`todo-mvc`

```bash
openyida create-page APP_XXX "仪表板" --mode dashboard
openyida generate-page product-homepage --spec .cache/openyida/page-specs/home.json --output pages/src/home.oyd.jsx --compile
openyida check-page pages/src/home.oyd.jsx
openyida compile pages/src/home.oyd.jsx
openyida publish pages/src/home.oyd.jsx APP_XXX FORM_XXX
```

### 审批流程配置

```bash
openyida create-process APP_XXX "采购申请" fields.json process.json
openyida configure-process APP_XXX FORM_XXX process.json
openyida process preview APP_XXX PROC_INST_XXX --output process.html
```

**流程 JSON 示例**：
```json
{
  "nodes": [{
    "type": "approval",
    "name": "主管审批",
    "approver": {
      "type": "user",
      "users": [{ "id": "manager7350", "name": "九神" }],
      "multiApproverType": "all"
    }
  }]
}
```

### 数据操作

```bash
openyida data query form APP_XXX FORM_XXX --page 1 --size 20
openyida data create form APP_XXX FORM_XXX --data-file record.json
```

> ⚠️ **重要**：日期字段必须使用 13 位毫秒时间戳

### 权限管理

```bash
openyida get-permission APP_XXX FORM_XXX
```

### 连接器与集成

```bash
openyida connector --help
```

### 完整命令速查

| 操作 | 命令 |
|-----|------|
| 环境检测 | `openyida env` |
| 登录 | `openyida login` |
| 创建应用 | `openyida create-app "应用名"` |
| 列出应用 | `openyida app-list` |
| 创建表单 | `openyida create-form APP_XXX "表单名" fields.json` |
| 查询数据 | `openyida data query form APP_XXX FORM_XXX` |
| 获取 Schema | `openyida get-schema APP_XXX FORM_XXX` |
| 版本查看 | `openyida --version` |
| 帮助 | `openyida --help` |

---

## 🤖 AI 工具集成

### 支持的编码工具

| 工具 | 支持程度 |
|-----|---------|
| Claude Code | 完整支持 |
| Codex | 完整支持 |
| Aone Copilot | 完整支持 |
| OpenCode | 完整支持 |
| Cursor | 完整支持 |
| Visual Studio Code | 完整支持 |
| QoderWork / Qoder | 完整支持 |
| Wukong（悟空） | 完整支持（需手动安装技能包） |

### AI 工作流最佳实践

#### 推荐对话示例

```
@宜搭 在宜搭里创建一个 CRM 应用，包含客户、联系人、商机、跟进记录表单
```

```
@宜搭 搭建一个芯片生产的 IPD 工作流，包含审批节点和仪表板页面
```

#### 临时文件管理

统一放在 `.cache/openyida/` 目录下

#### 工作目录

⚠️ **不要直接在 `C:\WINDOWS\system32` 下开发**

```powershell
cd ~/Desktop/your-project
openyida --version
```

### MCP 集成

#### 配置 MCP Server

```json
{
  "mcpServers": {
    "openyida": {
      "command": "npx",
      "args": ["-y", "@openyida/mcp-app", "--stdio"]
    }
  }
}
```

#### 可用工具

| 工具 | 功能 | 交互式 UI |
|-----|------|----------|
| `yida_list_apps` | 列出应用 | ✅ |
| `yida_create_app` | 创建应用 | — |
| `yida_get_schema` | 获取表单 Schema | ✅ |
| `yida_create_form` | 创建表单 | — |
| `yida_query_data` | 查询数据 | — |
| `yida_query_report` | 查询报表数据 | ✅ |

---

## 🏗️ 项目结构

```
openyida/
├── bin/yida.js                 # CLI 入口
├── lib/
│   ├── app/                    # 应用管理
│   ├── auth/                   # 登录认证
│   ├── connector/              # 连接器
│   ├── core/                   # 核心功能
│   ├── process/                # 流程管理
│   ├── report/                 # 报表生成
│   └── samples/                # 模板
├── project/                    # 工作区模板
├── yida-skills/                # 技能文档
└── scripts/                    # 脚本工具
```

### 各模块职责

| 模块 | 职责 |
|------|------|
| `bin/yida.js` | CLI 命令行入口 |
| `lib/app/` | 应用创建、更新、导入导出 |
| `lib/auth/` | 登录认证、会话管理 |
| `lib/connector/` | HTTP 连接器、集成管理 |
| `lib/core/` | 核心API调用、数据处理 |
| `lib/process/` | 流程表单、审批流配置 |
| `lib/report/` | 报表数据查询、生成 |
| `lib/samples/` | 内置模板、示例代码 |

---

## ❓ 常见问题

### 安装和环境

**Q: Node.js 版本过低，如何升级？**
A: 升级到 18 或以上版本。访问 https://nodejs.org/ 获取最新版本。

**Q: 安装后 openyida 命令找不到？**
A: 确保使用了 `-g` 全局安装参数。运行 `npm list -g openyida` 验证。

### 登录问题

**Q: 登录时 Playwright chromium 缺失？**
A: 这是常见的 Windows 问题。参考「安装与设置 > 故障排查」中的解决方案。

**Q: 登录二维码不显示？**
A: 尝试使用 `openyida login --qr` 强制使用终端二维码。

**Q: 如何检查登录状态？**
A: 运行 `openyida login --check-only --json` 查看当前登录状态。

### 数据操作

**Q: 日期字段提交失败？**
A: ⚠️ 日期字段必须使用 13 位毫秒时间戳，例如 `1633024800000`。

**Q: 如何查询表单数据？**
A: 使用 `openyida data query form APP_XXX FORM_XXX --page 1 --size 20`。

### AI 集成

**Q: 支持哪些 AI 工具？**
A: Claude Code、GPT 系列、Cursor、VS Code、Codex 等都支持。参考「AI 工具集成」部分。

---

## 📦 相关资源

- **项目主页**：https://github.com/orgs/yida-cli
- **NPM 包**：[openyida](https://www.npmjs.com/package/openyida)
- **MCP 包**：[@openyida/mcp-app](https://www.npmjs.com/package/@openyida/mcp-app)
- **文档**: 本项目 README
- **问题反馈**: [GitHub Issues](https://github.com/orgs/yida-cli/issues)

---

## 🎓 核心理念

1. **自然语言驱动** — 说人话，办正事
2. **分钟级交付** — 从需求到上线  
3. **人人都是开发者** — 业务人员也能构建应用

---

## 📝 许可与贡献

本项目由阿里巴巴钉钉团队维护。欢迎提交 Issue 和 Pull Request。

---

**最后更新**: 2026-10-06  
**版本**: openyida v1.0+
