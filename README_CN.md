# [niuma_code](https://niumacode.cn/)

[English](README.md) | [中文](README_CN.md)

> Claude Code 的补充工具 —— **减少上下文，倍增注意力**。让每个 LLM token 落在它该在的地方。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()
[![Version: 0.2.0](https://img.shields.io/badge/Version-0.2.0--alpha-green.svg)]()

---

## 目录

- [什么是 niuma_code？](#什么是-niuma_code)
- [快速开始](#快速开始)
- [核心特性](#核心特性)
  - [1. 🤖 Harness 自主聊天（默认模式）](#1--harness-自主聊天默认模式)
  - [2. 🔁 Loop 工程化编排](#2--loop-工程化编排)
  - [3. 🖥️ TUI 全屏模式](#3-️-tui-全屏模式)
  - [4. 💻 IDE 编排模式](#4--ide-编排模式)
  - [5. 🔌 LLM Provider 路由](#5--llm-provider-路由)
  - [6. 🗂️ 多上下文并行管理](#6-️-多上下文并行管理)
  - [7. 🕸️ 代码知识图谱](#7-️-代码知识图谱)
  - [8. 🧠 感知驱动持久记忆](#8-️-感知驱动持久记忆)
  - [9. 🎯 结果奖励追踪](#9--结果奖励追踪)
  - [10. 🔄 Sub-Agent 并行研究](#10--sub-agent-并行研究)
  - [11. 🔍 Web 搜索集成](#11--web-搜索集成)
  - [12. ✅ 智能确认系统](#12--智能确认系统)
  - [13. 🎯 意图分类前置分流](#13--意图分类前置分流)
  - [14. 🌐 国际化（i18n）](#14--国际化i18n)
- [最佳实践：Harness vs. Loop](#最佳实践harness-vs-loop)
- [命令参考](#命令参考)
- [Effort 级别](#effort-级别)
- [配置参考](#配置参考)
- [数据目录](#数据目录)
- [官方链接](#官方链接)
- [许可证](#许可证)

---

## 什么是 niuma_code？

niuma_code 是一个**终端工具**，为 Claude Code 用户提供更精细的上下文和注意力控制。打包为单个便携式 `niuma.exe` —— 下载、配置、运行，无需安装。

模型不缺注意力机制 —— 它缺的是把注意力放在对的地方。上下文越长，噪声越稀释真正关键的信息。**niuma_code 只做一件事：减少上下文，倍增注意力。**

---

## 快速开始

### 1. 下载

下载 [`niuma.exe`](https://niumacode.cn/download/niuma.exe) —— 无需安装。支持 Windows 10/11 版本 1809+。

### 2. 配置

创建 `~/.niuma/settings.json`：

```json
{
  "factories": [
    {
      "base_url": "https://api.anthropic.com",
      "api_key": "your-api-key",
      "options": ["claude-sonnet-4-6", "claude-opus-4-8"]
    }
  ]
}
```

### 3. 启动

```bash
niuma.exe
```

> TUI 全屏模式自动启动。输入 `/help` 查看命令列表。

---

## 核心特性

### 1. 🤖 Harness 自主聊天（默认模式）

默认模式 —— 无需命令。LLM 在 tool_use 循环中自主探索，边想边做，直到任务完成。

- 分层权限：只读操作无需确认，批量修改批量确认，破坏性操作逐个确认
- 文件写入保护 + 交互式审批
- API 错误自动重试、流式渲染、ESC 取消、长输出自动压缩
- 适合边界模糊、难以分解的任务，由 LLM 判断何时完成

---

### 2. 🔁 Loop 工程化编排

`/loop <目标>` 进入目标驱动的自循环：构建带验证命令的清单 → 逐任务执行 → 验证 → 失败自纠（3-strike 机制）。

- **Plan → Execute → Verify → Self-correct** 失败原因回灌
- 双闸：`MAX_ROUNDS=20` 任务上限 + `MAX_RETRIES=3` 自纠上限
- `--run` 模式（允许启动类命令验证）+ 超时降级策略
- 适合可分解、可验证的工程任务

---

### 3. 🖥️ TUI 全屏模式

基于 `prompt_toolkit` 3.x 全屏 UI + Rich 终端渲染。输入框固定底部，LLM 流式输出时可随时追加新消息 —— 内容不会被推走。

- 9 个模态浮层：模型设置、消息队列、上下文切换、对话管理、权限确认、标签管理、语言选择、IDE 草稿、记忆管理
- 鼠标拖拽选择、滚动浏览、左键释放自动复制、`Ctrl+滚轮` 缩放字体
- 实时状态栏：7 种状态（Idle / Thinking 思考预览 / Generating token 计数 / ToolExecuting / Compressing 进度条 / WaitingForCompression / Error 5 秒自动过期）

---

### 4. 💻 IDE 编排模式

全屏代码编辑器，用 Python 原生语法编写编排脚本。注入 `llm_call`、`llm_confirm`、`llm_judge` 等函数。

- `F5` 预览（AST 静态分析），`F6` 无人值守执行（自动审批工具权限）
- 安全沙箱 + 危险 import 黑名单（`os`/`subprocess`/`socket` 等）
- 独立会话追踪、草稿管理、多 IDE 并行支持（VS Code、Cursor、Windsurf）

---

### 5. 🔌 LLM Provider 路由

当前支持 **Anthropic**（Claude Opus 4.8、Claude Sonnet 4）。单一 `settings.json` 配置 —— 填入 API Key 和端点即可，按模型名自动路由，会话内通过 `/model` 自由切换。

- Anthropic API 流式传输、重试、缓存控制
- 会话级模型切换（`/model`）
- 单一来源配置：`~/.niuma/settings.json`

---

### 6. 🗂️ 多上下文并行管理

把单一对话扩展为 N 个并行上下文，各自独立维护对话历史、token 计数与压缩状态 —— 零交叉污染。

- LRU 淘汰 + 自动过期检测；淘汰的上下文归档保留，可恢复
- `resume_latest_context` 配置支持跨重启会话延续
- `/context` 可视化浮层切换；退出时异步生成摘要
- `/messages` 按单元多选 —— 删除 / 重排 / LLM 摘要合并

---

### 7. 🕸️ 代码知识图谱

内置 LanguageParser，通过 tree-sitter 支持 Python 和 Java 的自动结构分析（tree-sitter 不可用时回退正则）。

- 4 个只读查询工具：定位符号、文件依赖、引用、调用链
- 返回 `文件:行号` + 签名 —— 取代 grep 的大海捞针
- 三因子决策按需重建，避免每次启动全量解析

---

### 8. 🧠 感知驱动持久记忆

基于 [memory-palace](https://github.com/zhiguoliu111/memory-palace) 的持久化记忆 —— 10 种运行时事件实时转写为记忆：

| 事件类型 | 感知别名 | 写入内容 |
|----------|----------|----------|
| `read_file` | 眼感知 | 读过的文件、是否核心模块 |
| `write_file` | 身感知 | 修改的文件、变更类型与行数 |
| `ai_output` | 舌感知 | AI 回复摘要（action_type 分类） |
| `tool_call` | 鼻感知 | 工具调用、pytest 结果解析与测试状态 |
| `outcome` | 果感知 | 任务结果 + 奖励分数 |
| `user_message` | — | 用户输入 + 技术关键词 |
| `decision` | — | 决策问题、选项与理由 |
| `memory_directive` | — | 显式 remember / forget 指令 |
| `session_start` / `session_end` | — | 会话生命周期边界 |

- 贝叶斯时间衰减评分，LRU 淘汰
- 事实三元组 + 对话摘要，四层检索
- 下次会话自动注入系统提示词 —— 积累项目知识，不再从零开始

---

### 9. 🎯 结果奖励追踪

追踪任务目标的完整生命周期，基于完成质量计算 0.0–1.0 奖励分数。

- `OutcomePerceptionTracker` 监控文件变更、工具调用、重试、测试结果
- 自动纠正提示、成功/失败分类
- 奖励分数成为记忆中的复用价值评估信号

---

### 10. 🔄 Sub-Agent 并行研究

只读子代理在隔离上下文中并行执行工具调用，返回摘要后用完即焚。

- 工具白名单：仅 `read_file` / `glob_files` / `grep_content` / `kg_*` + `report_result`
- 重试策略：消息续跑 + 失败分流（瞬时故障 → 重试，确定性故障 → 止损）
- 多个子 agent 并行运行，不阻塞主对话

---

### 11. 🔍 Web 搜索集成

通过 AnySearch API 在编码会话中实时检索。支持通用 Web 搜索和代码文档搜索（`code.doc` / `code.gh`），返回结构化 Markdown。

---

### 12. ✅ 智能确认系统

3 级审批：只读操作自动通过，批量修改批量确认，破坏性或未知操作逐个确认。白名单支持精确匹配和 glob 参数模式匹配（如 `execute_bash(git *)`）。黑名单作为底线兜底。

---

### 13. 🎯 意图分类前置分流

纯正则分类器（<1ms，无 jieba 依赖）将用户消息分为三路：feedback / command / question。反馈和斜杠命令直接处理，不进 LLM，从源头省 token。

---

### 14. 🌐 国际化（i18n）

基于 JSON 词表的国际化系统，从 `app/i18n/locales/` 自动发现语言文件。`/lang` 命令运行时切换界面语言（中/英文），同时控制 LLM 应答语言。

---

## 最佳实践：Harness vs. Loop

| | Harness（默认） | Loop（`/loop <目标>`） |
|---|---|---|
| **何时使用** | 大致有想法，说不清步骤，边做边迭代 | 任务可分解为步骤，每步有可验证的检查 |
| **示例** | "找到登录页的报错并修复" | `/loop 给用户 API 加分页，每次改动后运行 py_compile` |
| **关键** | 先列出约束：目标 + 不要动的部分 + 验收标准 | 目标必须可分解，且附带验证命令 |
| **停止信号** | 随时 ESC；"声称完成" ≠ 真的完成 —— 自己跑测试 | 连续 3 次纠正失败 = 信息不足，拆得更细 |

---

## 命令参考

| 命令 | 说明 |
|------|------|
| `/model` | 打开模型选择弹框 |
| `/lang` | 切换界面语言（无参打开弹框） |
| `/clear` | 清空对话历史 |
| `/compact` | 手动压缩上下文（--recall 仅清记忆注入） |
| `/recall` | 开关记忆召回（默认开启）[Ctrl+R] |
| `/memory` | 记忆管理（浏览/搜索/删除跨会话记忆） |
| `/loop <目标>` | 工程化任务循环（规划→执行→验证→失败自纠） |
| `/permission` | 权限设置，自动持久化（mode/allow/deny） |
| `/copy [-file <path>]` | 复制 Markdown 到剪贴板或文件 [Ctrl+P] |
| `/ls [pattern]` | 列出文件（支持模糊搜索） |
| `/cat <file>` | 读取文件内容 |
| `/edit` | 打开系统编辑器 [Ctrl+G] |
| `/refactor [file]` | 重构代码 |
| `/ide` | 进入 Python 编排模式 [Ctrl+I] |
| `/new` | 开启新话题（标记边界，之前的历史不注入） |
| `/context` | 多上下文切换（new [名称] / rename [名称] / <序号> / del <序号>） |
| `/messages` | 对话管理（多选/删除/合并/移位） |
| `/star` | 收藏/取消当前对话轮次 |
| `/tag` | 自定义 #标签管理（无参弹框 / add/mod/rm/ls） |
| `/help` | 显示帮助 |
| `/restart` | 重启服务 |
| `/quit` / `/exit` | 退出程序 [Ctrl+D] |

---

## Effort 级别

| 级别 | Thinking | 适用场景 |
|------|----------|----------|
| `low` | 关闭 | 快速闲聊、简单问答、快速响应 |
| `medium` | 关闭 | 一般编程任务 |
| `high` | 10k tokens | 复杂重构、架构设计 |
| `max` | 20k tokens | 深度分析、多步骤推理 |

**注意**：Effort 仅控制 thinking 深度，**不切换模型**。模型通过 `/model` 命令手动切换，切换会导致 prompt cache 失效，增加 token 成本。

---

## 配置参考

配置文件路径：

`~/.niuma/settings.json`

<details>
<summary><strong>factories — Anthropic API 配置</strong></summary>

```json
{
  "factories": [
    {
      "base_url": "https://api.anthropic.com",
      "api_key": "sk-ant-xxx",
      "options": ["claude-sonnet-4-6", "claude-opus-4-8"]
    }
  ]
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `base_url` | `string` | — | API 端点 URL（必填） |
| `api_key` | `string` | — | API Key（必填） |
| `options` | `string[]` | `[]` | 该端点支持的模型列表 |

</details>

<details>
<summary><strong>llm — 模型、effort、thinking budget & 上下文管理</strong></summary>

```json
{
  "llm": {
    "default_model": "claude-sonnet-4-6",
    "default_effort": "high",
    "max_contexts": 5,
    "resume_latest_context": false,
    "thinking_budget_high": "10000",
    "thinking_budget_max": "20000"
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `default_model` | `string` | `"claude-sonnet-4-6"` | 默认模型（通过 `/model` 切换） |
| `default_effort` | `string` | `"high"` | 默认 effort 级别 — 控制 thinking budget |
| `max_contexts` | `integer` | `5` | 最大并行上下文数；LRU 淘汰最久未活跃者 |
| `resume_latest_context` | `boolean` | `false` | 启动时是否恢复上次 active 上下文 |
| `options` | `list` | `[]` | 模型+Effort 合法组合（供 /model 校验） |
| `thinking_budget_low` | `string/int` | `"0"` | low effort 的 thinking tokens（0 = 禁用） |
| `thinking_budget_medium` | `string/int` | `"0"` | medium effort 的 thinking tokens |
| `thinking_budget_high` | `string/int` | `"10000"` | high effort 的 thinking tokens |
| `thinking_budget_max` | `string/int` | `"20000"` | max effort 的 thinking tokens（最深推理） |

</details>

<details>
<summary><strong>env — 环境变量（人格、调试、网络、权限、语言）</strong></summary>

```json
{
  "env": {
    "PERSONA_NAME": "niuma",
    "MODEL_BACKGROUND": "claude-haiku-4-5",
    "LANG": "zh",
    "TEMPERATURE_ZERO": "true",
    "API_TIMEOUT": "30",
    "API_ROUND_MAX": "120",
    "PERMISSION_MODE": "auto"
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `PERSONA_NAME` | `string` | `"niuma"` | System prompt 中的人格名 |
| `MODEL_BACKGROUND` | `string` | `"claude-haiku-4-5"` | 后台任务模型（记忆提取、压缩） |
| `LANG` | `string` | `"zh"` | 界面语言（`zh`/`en`），同时控制 LLM 应答语言 |
| `TEMPERATURE_ZERO` | `string(bool)` | `"true"` | 固定 temperature=0 确定性输出 |
| `API_TIMEOUT` | `string/int` | `"30"` | 流式停滞超时（秒） |
| `API_ROUND_MAX` | `string/int` | `"120"` | 单轮请求墙钟硬上限（秒） |
| `PERMISSION_MODE` | `string` | `"auto"` | `auto` = 白名单+确认 / `manual` = 仅白名单 / `skip` = 全通过 |

</details>

<details>
<summary><strong>compact — 上下文压缩配置</strong></summary>

```json
{
  "compact": {
    "inline_trigger": 0.8,
    "inline_keep_ratio": 0.4,
    "idle_trigger": 0.5,
    "idle_keep_ratio": 0.4,
    "max_summary_tokens": 4096,
    "auto_recall_clear": false
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `inline_trigger` | `float` | `0.8` | 对话中压缩触发阈值（阻塞主循环） |
| `inline_keep_ratio` | `float` | `0.4` | 对话中压缩后保留消息比例 |
| `idle_trigger` | `float` | `0.5` | 空闲压缩触发阈值（异步，答完后执行） |
| `idle_keep_ratio` | `float` | `0.4` | 空闲压缩后保留消息比例 |
| `max_summary_tokens` | `integer` | `4096` | LLM 摘要最大输出 token 数 |
| `auto_recall_clear` | `boolean` | `false` | 每轮提问前是否自动清空上一轮 recall 注入 |

</details>

<details>
<summary><strong>memory_quality — 记忆质量门控</strong></summary>

```json
{
  "memory_quality": {
    "min_recall_score": 0.35,
    "dedup_threshold": 0.3
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `min_recall_score` | `float` | `0.35` | 召回分数低于此值不注入 prompt |
| `dedup_threshold` | `float` | `0.3` | Jaccard 字符相似度高于此值视为重复 |

</details>

<details>
<summary><strong>memory_palace — 记忆提取配置</strong></summary>

```json
{
  "memory_palace": {
    "enable_v9": true,
    "llm_enabled": false,
    "base_url": "",
    "api_key": "",
    "model": ""
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `enable_v9` | `boolean` | `true` | 启用 V9 感知驱动记忆；`false` = 旧版 V8 |
| `llm_enabled` | `boolean` | `false` | 启用 LLM 增强提取；`false` = 仅规则提取 |
| `base_url` | `string` | `""` | 记忆 LLM 端点（`llm_enabled: true` 时必填） |
| `api_key` | `string` | `""` | 记忆 LLM API Key |
| `model` | `string` | `""` | 记忆 LLM 模型（建议轻量模型） |

</details>

<details>
<summary><strong>anysearch — Web 搜索配置</strong></summary>

```json
{
  "anysearch": {
    "api_key": "",
    "base_url": "https://api.anysearch.com",
    "max_results": 5,
    "timeout": 15
  }
}
```

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `api_key` | `string` | `""` | AnySearch API Key |
| `base_url` | `string` | `"https://api.anysearch.com"` | API 端点 |
| `max_results` | `integer` | `5` | 最大搜索结果数 |
| `timeout` | `integer` | `15` | 搜索超时（秒） |

</details>

---

## 数据目录

```
~/.niuma/projects/{projectName}/
├── session/                 # 会话数据
│   ├── {sid}.json           # 对话历史
│   ├── {sid}_tasks.json     # 任务状态
│   ├── {sid}_trace.json     # 执行追踪
│   ├── {sid}_timeline.txt   # 追踪摘要
│   └── input.history        # 输入历史（临时，退出清理）
├── memory/                  # 记忆数据库
│   ├── memory-palace.db     # 记忆存储（SQLite）
│   └── niuma_behavior.db    # 行为日志（独立，避免锁竞争）
└── ide/                     # IDE 草稿
    └── {name}.py            # 编排脚本草稿
```

---

## 官方链接

- **官网：** [niumacode.cn](https://niumacode.cn/)
- **GitHub：** [github.com/zhiguoliu111/niuma_code](https://github.com/zhiguoliu111/niuma_code)
- **Issues：** [github.com/zhiguoliu111/niuma_code/issues](https://github.com/zhiguoliu111/niuma_code/issues)
- **教程：** [niumacode.cn/tutorials](https://niumacode.cn/tutorials/)
- **下载：** [niuma.exe](https://niumacode.cn/download/niuma.exe)

---

## 许可证

MIT License © 2026 niuma_code
