# OpenYida 命令教程

> 快速学会用 OpenYida 构建宜搭应用的完整命令指南

---

## 📖 目录

- [🚀 5分钟快速上手](#5分钟快速上手)
- [💻 完整命令参考](#完整命令参考)
  - [应用管理](#应用管理)
  - [表单操作](#表单操作)
  - [页面开发](#页面开发)
  - [流程配置](#流程配置)
  - [数据查询](#数据查询)
  - [权限管理](#权限管理)
- [🎯 实战场景](#实战场景)
- [💡 最佳实践](#最佳实践)
- [❓ 命令速查](#命令速查)

---

## 🚀 5分钟快速上手

### 第1步：安装

```bash
npm install -g openyida
```

### 第2步：检查环境

```bash
# 基础环境检测
openyida env

# 获取 JSON 格式输出
openyida env --json

# 查看所有可用命令（供 AI 读取）
openyida commands --json
```

### 第3步：登录宜搭

```bash
# 自动登录（自动尝试 Chrome/Edge，或弹出二维码）
openyida login

# 强制使用终端二维码
openyida login --qr

# 指定企业登录
openyida login --qr --corp-id ding8ecb6241a2974bb935c2f4657eb6378f

# 检查登录状态
openyida login --check-only --json
```

### 第4步：验证成功

```bash
# 列出当前账号的应用
openyida app-list

# 查看版本
openyida --version

# 获取帮助
openyida --help
```

✅ 完成！你现在可以开始构建应用了。

---

## 💻 完整命令参考

### 应用管理

#### 创建应用

```bash
# 最简单的方式
openyida create-app "CRM"

# 完整参数
openyida create-app --name "CRM" --desc "客户关系管理" --theme deepBlue
```

**参数说明**：
- `--name`: 应用名称（必填）
- `--desc`: 应用描述
- `--theme`: 主题色（可选值：deepBlue, lightBlue, green, red 等）

#### 列表应用

```bash
# 默认列出所有应用
openyida app-list

# 限制数量
openyida app-list --size 20

# 分页查询
openyida app-list --page 1 --size 10
```

#### 企业效率看板

```bash
# 查看企业内应用统计
openyida corp-efficiency
```

---

### 表单操作

#### 创建表单

```bash
# 从 JSON 文件创建表单
openyida create-form APP_ID "表单名称" fields.json

# 更新已有表单
openyida create-form update APP_ID FORM_ID fields-changes.json
```

**fields.json 示例**：
```json
{
  "fields": [
    {
      "fieldName": "customerName",
      "label": "客户名称",
      "type": "text",
      "required": true
    },
    {
      "fieldName": "contactPhone",
      "label": "联系电话",
      "type": "phone",
      "required": true
    },
    {
      "fieldName": "industry",
      "label": "所属行业",
      "type": "select",
      "options": ["IT", "金融", "制造", "零售"]
    },
    {
      "fieldName": "createTime",
      "label": "创建时间",
      "type": "datetime"
    }
  ]
}
```

#### 获取表单 Schema

```bash
# 获取单个表单的 Schema
openyida get-schema APP_ID FORM_ID

# 获取应用内所有表单的 Schema
openyida get-schema APP_ID --all

# 输出到文件
openyida get-schema APP_ID --all --output-dir ./schemas
```

#### 获取表单权限

```bash
openyida get-permission APP_ID FORM_ID
```

---

### 页面开发

#### 创建页面

```bash
# 创建仪表板页面
openyida create-page APP_ID "销售仪表板" --mode dashboard

# 创建表单页面
openyida create-page APP_ID "客户详情" --mode form
```

#### 从模板生成页面

```bash
# 使用内置模板生成
openyida generate-page product-homepage \
  --spec ./page-specs/home.json \
  --output ./pages/src/home.oyd.jsx \
  --compile

# 参数说明
# product-homepage: 模板名（可选值：product-homepage, todo-mvc）
# --spec: 页面配置文件
# --output: 输出文件路径
# --compile: 生成后立即编译
```

**page-specs/home.json 示例**：
```json
{
  "title": "产品首页",
  "layout": "grid",
  "components": [
    {
      "type": "header",
      "title": "欢迎使用 OpenYida"
    },
    {
      "type": "card",
      "title": "快速开始",
      "content": "..."
    }
  ]
}
```

#### 检查页面代码

```bash
openyida check-page pages/src/home.oyd.jsx
```

#### 编译页面

```bash
# 编译单个页面
openyida compile pages/src/home.oyd.jsx

# 编译输出到指定位置
openyida compile pages/src/home.oyd.jsx --output dist/
```

#### 发布页面

```bash
# 发布到应用中的表单
openyida publish pages/src/home.oyd.jsx APP_ID FORM_ID

# 发布多个页面
openyida publish pages/src/*.oyd.jsx APP_ID FORM_ID
```

---

### 流程配置

#### 创建审批流程

```bash
# 创建流程表单
openyida create-process APP_ID "采购申请流程" \
  fields.json \
  process.json
```

**process.json 示例**：
```json
{
  "processName": "采购审批流程",
  "nodes": [
    {
      "type": "start",
      "name": "开始"
    },
    {
      "type": "approval",
      "name": "部门主管审批",
      "approver": {
        "type": "user",
        "users": [
          {
            "id": "manager7350",
            "name": "张三"
          }
        ],
        "multiApproverType": "all"
      }
    },
    {
      "type": "approval",
      "name": "财务审批",
      "approver": {
        "type": "role",
        "roles": ["finance-manager"]
      }
    },
    {
      "type": "end",
      "name": "结束"
    }
  ],
  "transitions": [
    {
      "from": "start",
      "to": "部门主管审批"
    },
    {
      "from": "部门主管审批",
      "to": "财务审批",
      "condition": "amount > 10000"
    },
    {
      "from": "财务审批",
      "to": "end"
    }
  ]
}
```

#### 配置流程

```bash
openyida configure-process APP_ID FORM_ID process.json
```

#### 预览流程

```bash
# 预览流程实例
openyida process preview APP_ID PROC_INST_ID

# 输出为 HTML 文件
openyida process preview APP_ID PROC_INST_ID --output process.html
```

---

### 数据查询

#### 查询表单数据

```bash
# 查询表单数据（默认第1页）
openyida data query form APP_ID FORM_ID

# 分页查询
openyida data query form APP_ID FORM_ID --page 1 --size 20

# 指定查询字段
openyida data query form APP_ID FORM_ID --fields "name,email,phone"

# 添加过滤条件
openyida data query form APP_ID FORM_ID --filter "status=active"

# 排序
openyida data query form APP_ID FORM_ID --sort "createTime:desc"
```

#### 创建数据记录

```bash
# 从文件创建记录
openyida data create form APP_ID FORM_ID --data-file record.json

# 批量创建
openyida data create form APP_ID FORM_ID --data-file records.json --batch
```

**record.json 示例**：
```json
{
  "customerName": "阿里巴巴",
  "contactPhone": "13800138000",
  "industry": "IT",
  "createTime": 1633024800000
}
```

⚠️ **重要**：日期字段必须使用 **13 位毫秒时间戳**，例如 `1633024800000`。

#### 查询流程数据

```bash
openyida data query process APP_ID FORM_ID --page 1 --size 20
```

#### 查询任务数据

```bash
openyida data query task APP_ID --page 1 --size 20
```

#### 查询子表单数据

```bash
openyida data query subform APP_ID FORM_ID SUB_FORM_ID --page 1 --size 20
```

---

### 权限管理

#### 获取权限信息

```bash
# 查看表单权限
openyida get-permission APP_ID FORM_ID

# 输出为 JSON
openyida get-permission APP_ID FORM_ID --json
```

---

### 连接器与集成

#### 连接器帮助

```bash
# 查看连接器相关命令
openyida connector --help
```

---

## 🎯 实战场景

### 场景1：快速创建 CRM 应用

```bash
# 1. 创建应用
APP_ID=$(openyida create-app "销售 CRM" | grep -oP '"appId":"\K[^"]+')

# 2. 创建客户信息表
openyida create-form $APP_ID "客户信息" customer-fields.json

# 3. 创建联系人表
openyida create-form $APP_ID "联系人" contact-fields.json

# 4. 查看结果
openyida app-list
```

### 场景2：构建审批流程

```bash
# 1. 创建审批流程应用
openyida create-app "采购审批系统"

# 2. 创建流程（包含表单和审批节点）
openyida create-process APP_ID "采购单审批" \
  purchase-fields.json \
  purchase-workflow.json

# 3. 预览流程
openyida process preview APP_ID PROC_INST_ID --output preview.html

# 4. 打开预览
# open preview.html （在浏览器中打开）
```

### 场景3：数据分析与报表

```bash
# 1. 查询所有销售数据
openyida data query form APP_ID FORM_ID \
  --fields "name,amount,createTime" \
  --sort "amount:desc" \
  --page 1 \
  --size 100

# 2. 导出为 JSON（配合 jq 工具）
openyida data query form APP_ID FORM_ID --json | jq '.' > sales.json

# 3. 分析数据
cat sales.json | jq '[.[].amount] | add'  # 统计总额
```

---

## 💡 最佳实践

### 1. 环境管理

```bash
# 每次开始新项目先检查环境
openyida env --json

# 保存环境信息便于复查
openyida env --json > env-backup.json
```

### 2. 文件组织

推荐的项目结构：

```
my-yida-project/
├── .cache/
│   └── openyida/
│       ├── forms/              # 表单定义
│       ├── processes/          # 流程定义
│       ├── pages/              # 页面代码
│       ├── schemas/            # 表单 Schema 备份
│       └── data/               # 数据文件
├── src/
│   └── pages/                  # React 页面源码
└── README.md
```

### 3. 日期字段处理

```bash
# 获取当前时间戳（毫秒）
node -e "console.log(Date.now())"

# 转换日期为时间戳
node -e "console.log(new Date('2024-10-06').getTime())"

# 在 JSON 中使用
cat > record.json << EOF
{
  "name": "张三",
  "createTime": $(node -e "console.log(Date.now())")
}
EOF
```

### 4. 批量操作脚本

```bash
#!/bin/bash
# 批量创建表单

FORMS=("客户" "订单" "发票" "收款")

for form in "${FORMS[@]}"; do
  echo "创建表单: $form"
  openyida create-form $APP_ID "$form" "forms/${form}.json"
done
```

### 5. 与 AI 工具集成

配合 Claude Code 使用时，先输出命令清单供 AI 读取：

```bash
# 让 AI 获取完整命令列表
openyida commands --json > commands.json

# 然后告诉 AI：
# "这是 openyida 的完整命令列表：$(cat commands.json)"
```

---

## ❓ 命令速查

| 功能 | 命令 |
|------|------|
| **环境管理** | |
| 检查环境 | `openyida env` |
| 检查环境（JSON） | `openyida env --json` |
| 查看所有命令 | `openyida commands --json` |
| **登录** | |
| 登录 | `openyida login` |
| 二维码登录 | `openyida login --qr` |
| 指定企业登录 | `openyida login --qr --corp-id dingXXX` |
| 检查登录状态 | `openyida login --check-only --json` |
| **应用管理** | |
| 创建应用 | `openyida create-app "应用名"` |
| 列出应用 | `openyida app-list` |
| 企业效率看板 | `openyida corp-efficiency` |
| **表单操作** | |
| 创建表单 | `openyida create-form APP_ID "表单名" fields.json` |
| 更新表单 | `openyida create-form update APP_ID FORM_ID changes.json` |
| 获取 Schema | `openyida get-schema APP_ID FORM_ID` |
| 获取所有 Schema | `openyida get-schema APP_ID --all` |
| 获取权限 | `openyida get-permission APP_ID FORM_ID` |
| **页面开发** | |
| 创建页面 | `openyida create-page APP_ID "页面名" --mode dashboard` |
| 从模板生成 | `openyida generate-page product-homepage --spec spec.json --output out.jsx --compile` |
| 检查页面 | `openyida check-page pages/src/home.oyd.jsx` |
| 编译页面 | `openyida compile pages/src/home.oyd.jsx` |
| 发布页面 | `openyida publish pages/src/home.oyd.jsx APP_ID FORM_ID` |
| **流程配置** | |
| 创建流程 | `openyida create-process APP_ID "流程名" fields.json process.json` |
| 配置流程 | `openyida configure-process APP_ID FORM_ID process.json` |
| 预览流程 | `openyida process preview APP_ID PROC_INST_ID` |
| **数据操作** | |
| 查询表单数据 | `openyida data query form APP_ID FORM_ID` |
| 创建数据 | `openyida data create form APP_ID FORM_ID --data-file record.json` |
| 查询流程数据 | `openyida data query process APP_ID FORM_ID` |
| 查询任务 | `openyida data query task APP_ID` |
| 查询子表单 | `openyida data query subform APP_ID FORM_ID SUB_FORM_ID` |
| **其他** | |
| 查看版本 | `openyida --version` |
| 查看帮助 | `openyida --help` |

---

## 📚 扩展资源

- 完整文档：参考 `README.md`
- 配置指南：参考 `README.md` 中的「AI 工具集成」
- 问题排查：参考 `README.md` 中的「常见问题」

---

**最后更新**: 2026-10-06  
**适用版本**: OpenYida v1.0+
