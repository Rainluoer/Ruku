# ruku — 入库单自动生成

> 科研材料入库单自动填写工具。读取发票 PDF 提取商品信息，按内容重命名发票文件，基于模板自动生成入库单。

![Python](https://img.shields.io/badge/python-3.8+-blue) ![License](https://img.shields.io/badge/license-MIT-green)

---

## 功能

- **PDF 发票解析** — 自动提取商品类目、品名、规格、单位、数量、单价、金额、税率、税额
- **智能重命名** — 按 `发票-序号-类目品名-金额.pdf` 规范重命名文件
- **一键生成入库单** — 基于 Word 模板填写，支持大写金额、合计行、行数自适应
- **含税价处理** — 多商品发票逐项计算含税价，单商品发票直接取价税合计
- **输出结果到发票目录** — 入库单直接生成在发票所在目录下
- **严格流程** — 智能体严格按三步骤执行，无额外干预

## 安装

将项目目录放置到工作区中，告诉你的 AI 编码助手：

> 请安装 ruku skill

或直接描述需求：

> 帮我整理发票，生成入库单

助手会自动发现并加载本 skill。

## 快速使用

将发票 PDF 放入一个目录（如 `发票/`），然后告诉助手：

> 生成入库单

助手将自动执行完整流程：

```
步骤1 → 提取发票信息 → 输出摘要
步骤2 → 重命名发票文件 → 输出摘要
步骤3 → 生成入库单 → 输出最终汇总
```

也可手动运行各步骤：

```bash
# 1. 提取发票信息
python scripts/extract_invoices.py --invoice_dir="./发票" --output="./发票/../temp/data.json"

# 2. 重命名发票文件
python scripts/rename_files.py --invoice_dir="./发票" --data="./发票/../temp/data.json"

# 3. 生成入库单到发票目录
python scripts/fill_receipt.py --data="./发票/../temp/data.json" --output="./发票/入库单_已填写.docx"
```

运行后 `temp/` 目录自动清理，入库单保存在发票目录下。

> 商品信息填写完成后，请手动补全项目名称、项目代号、验收人、保管人、日期。

## 目录结构

```
ruku/
├── SKILL.md                  # Skill 配置、工作流程及约束规则
├── README.md
├── template/
│   └── 模板.docx             # 入库单 Word 模板
└── scripts/
    ├── extract_invoices.py   # PDF 发票提取
    ├── rename_files.py       # 按内容重命名
    └── fill_receipt.py       # 填写入库单
```

## 执行流程

| 步骤 | 脚本 | 功能 | 输出 |
|------|------|------|------|
| 1 | `extract_invoices.py` | 读取所有 PDF，提取商品信息 | JSON 数据文件 |
| 2 | `rename_files.py` | 按规范重命名 PDF 文件 | 更新文件名 |
| 3 | `fill_receipt.py` | 基于模板填写入库单 | `入库单_已填写.docx` |

**约束规则**（智能体必须遵守）：
- 严格按 1→2→3 顺序执行，不可跳步或合并
- 禁止修改脚本代码
- 入库单必须输出到发票目录下
- 不允许添加额外步骤或输出分析建议

## 依赖

| 依赖 | 用途 | 说明 |
|------|------|------|
| AI 编码助手 | 自动加载 SKILL.md 并按指令执行流程 | 支持 OpenCode / Claude Code / Codex 等 |
| Python 3.8+ | 运行环境 | 手动安装 |
| pdfplumber | 读取发票 PDF | 脚本自动安装 |
| python-docx | 读写 Word 入库单 | 脚本自动安装 |

缺失的 Python 依赖在首次运行时自动 `pip install`，无需手动操作。

## 入库单模板

模板文件 `template/模板.docx` 包含两个表格：

- **Table[0]**：项目信息（名称、代码），自动保留
- **Table[1]**：入库单主表（表头 + 数据行 + 合计行 + 签名栏）

默认支持最多 7 项商品，超出时自动插入新行。

## 许可证

MIT
