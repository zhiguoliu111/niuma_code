# [niuma_code](https://niumacode.cn/)

[English](README.md) | [中文](README_CN.md)

> A supplementary tool for Claude Code — **subtract context, multiply attention**. Every LLM token lands exactly where it matters.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()
[![Version: 0.2.0](https://img.shields.io/badge/Version-0.2.0--alpha-green.svg)]()

---

## Table of Contents

- [What is niuma_code?](#what-is-niuma_code)
- [Quick Start](#quick-start)
- [Core Features](#core-features)
  - [🤖 Harness Autonomous Chat](#-harness-autonomous-chat-default-mode)
  - [🔁 Loop Engineered Orchestration](#-loop-engineered-orchestration)
  - [🖥️ TUI Full-Screen Mode](#️-tui-full-screen-mode)
  - [💻 IDE Orchestration Mode](#-ide-orchestration-mode)
  - [🔌 LLM Provider Routing](#-llm-provider-routing)
  - [🗂️ N-Context Parallel Management](#️-n-context-parallel-management)
  - [🕸️ Code Knowledge Graph](#️-code-knowledge-graph)
  - [🧠 Perception-Driven Persistent Memory](#-perception-driven-persistent-memory)
  - [🎯 Outcome Reward Tracking](#-outcome-reward-tracking)
  - [🔄 Sub-Agent Parallel Research](#-sub-agent-parallel-research)
  - [🔍 Web Search Integration](#-web-search-integration)
  - [✅ Smart Confirmation System](#-smart-confirmation-system)
  - [🎯 Intent Classification](#-intent-classification)
  - [🌐 Internationalization (i18n)](#-internationalization-i18n)
- [Best Practices: Harness vs. Loop](#best-practices-harness-vs-loop)
- [Command Reference](#command-reference)
- [Effort Levels](#effort-levels)
- [Configuration Reference](#configuration-reference)
- [Data Directory](#data-directory)
- [Links](#links)
- [License](#license)

---

## What is niuma_code?

niuma_code is a **terminal tool** for Claude Code users who want tighter control over context and attention. Packaged as a single portable `niuma.exe` — download, configure, run. No installation.

Models don't lack attention mechanisms — they lack placing attention where it matters. The longer the context, the more noise dilutes truly critical information. **niuma_code does one thing: subtract context, multiply attention.**

---

## Quick Start

### 1. Download

Download [`niuma.exe`](https://niumacode.cn/download/niuma.exe) — no install needed. Supports Windows 10/11 version 1809+.

### 2. Configure

Create `~/.niuma/settings.json`:

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

### 3. Launch

```bash
niuma.exe
```

> The TUI full-screen mode launches automatically. Type `/help` for a list of commands.

---

## Core Features

### 🤖 Harness Autonomous Chat (Default Mode)

The default mode — no commands needed. LLM explores autonomously in a tool_use loop, thinking and acting simultaneously until the task is done.

- Layered permissions: no-confirm for read-only, batch-confirm for bulk fixes, per-confirm for destructive ops
- File-write protection with interactive approval
- API error auto-retry, streaming render, ESC cancel, auto-compression for long outputs
- Best for fuzzy, hard-to-decompose tasks where LLM judges when it's done

---

### 🔁 Loop Engineered Orchestration

`/loop <goal>` enters a goal-driven self-loop: build a checklist with verification commands, execute sequentially, verify each step, and self-correct on failure with a 3-strike mechanism.

- **Plan → Execute → Verify → Self-correct** with failure reason back-feed
- Dual gates: `MAX_ROUNDS=20` task cap + `MAX_RETRIES=3` self-correction
- `--run` mode for startup command verification + timeout degradation
- Ideal for decomposable, verifiable engineering tasks

---

### 🖥️ TUI Full-Screen Mode

Based on `prompt_toolkit` 3.x full-screen UI with Rich terminal rendering. Input fixed at the bottom, append new messages anytime during LLM streaming — no content gets pushed away.

- 9 modal overlays: model settings, message queue, context switch, conversation management, permission confirmation, tag management, language selection, IDE drafts, memory management
- Mouse drag selection, scroll browsing, left-click auto-copy, `Ctrl+Scroll` font zoom
- Real-time status bar: 7 states (Idle / Thinking preview / Generating token counts / ToolExecuting / Compressing progress / WaitingForCompression / Error auto-expire)

---

### 💻 IDE Orchestration Mode

Full-screen code editor with Python-native syntax for orchestration scripts. Write scripts using injected `llm_call`, `llm_confirm`, and `llm_judge` functions.

- `F5` preview (AST static analysis), `F6` unattended execution (auto-approve tool permissions)
- Secure sandbox + dangerous import blacklist (`os`/`subprocess`/`socket` etc.)
- Per-IDE session tracking, draft management, parallel IDE support (VS Code, Cursor, Windsurf)

---

### 🔌 LLM Provider Routing

Currently supports **Anthropic** (Claude Opus 4.8, Claude Sonnet 4). Single-source `settings.json` configuration — configure your API key and endpoint, auto-route by model name, switch freely within a session via `/model`.

- Anthropic API with streaming, retry, and cache control
- Session-level model switching via `/model`
- Single-source config: `~/.niuma/settings.json`

---

### 🗂️ N-Context Parallel Management

Extend a single conversation into N parallel contexts, each independently maintaining history, token counts, and compression state — zero cross-contamination.

- LRU eviction with automatic staleness detection; evicted contexts are archived and recoverable
- `resume_latest_context` config for session continuation across restarts
- `/context` visual overlay for switching; async summary generation on exit
- `/messages` multi-select by unit — delete / reorder / LLM summary merge

---

### 🕸️ Code Knowledge Graph

Built-in LanguageParser supporting Python and Java via tree-sitter for automatic structural analysis (regex fallback when tree-sitter unavailable).

- 4 read-only query tools: locate symbols, file dependencies, references, call chains
- Returns `file:line` with signatures — replaces grep's needle-in-a-haystack
- Three-factor decision for on-demand rebuild, avoiding full parse on every launch

---

### 🧠 Perception-Driven Persistent Memory

Persistent memory powered by [memory-palace](https://github.com/zhiguoliu111/memory-palace) — 10 types of runtime events are transcribed into memory in real-time:

| Event Type | Perception Alias | Content |
|------------|-----------------|---------|
| `read_file` | Eye | Files read, core module detection |
| `write_file` | Body | Files modified, change type & line count |
| `ai_output` | Tongue | AI reply summary (action_type classification) |
| `tool_call` | Nose | Tool calls, pytest result parsing & test status |
| `outcome` | Outcome | Task result with reward score |
| `user_message` | — | User input + tech keywords |
| `decision` | — | Decision questions, options & reasoning |
| `memory_directive` | — | Explicit remember / forget instructions |
| `session_start` / `session_end` | — | Session lifecycle boundaries |

- Bayesian time-decay scoring, LRU eviction
- Fact triples + conversation summaries, 4-layer retrieval
- Auto-injected into system prompt on next session — accumulates project knowledge, no more starting from zero

---

### 🎯 Outcome Reward Tracking

Tracks the full lifecycle of task objectives, computes 0.0–1.0 reward scores based on completion quality.

- `OutcomePerceptionTracker` monitors files touched, tool calls, retries, test results
- Auto-correction prompts, success/failure classification
- Reward scores become reuse-value evaluation signals in memory

---

### 🔄 Sub-Agent Parallel Research

Read-only sub-agents execute tools in parallel within isolated contexts, returning summaries after research.

- Tool whitelist: `read_file` / `glob_files` / `grep_content` / `kg_*` + `report_result` only
- Retry strategy: message continuation + failure triage (transient → retry, deterministic → stop)
- Multiple sub-agents run non-blocking, no main conversation pollution

---

### 🔍 Web Search Integration

Real-time information retrieval via AnySearch API during coding sessions. Supports general web search and code documentation search (`code.doc` / `code.gh`), returning structured Markdown.

---

### ✅ Smart Confirmation System

3-tier approval: no-confirm for read-only operations (auto-approved), batch-confirm for bulk fixes, per-confirm for destructive or unknown operations. Whitelist supports exact match and glob parameter patterns (e.g., `execute_bash(git *)`). Blacklist as bottom-line guard in all modes.

---

### 🎯 Intent Classification

Pure regex classifier (<1ms, no jieba dependency) routes user messages into three paths: feedback / command / question. Feedback and slash commands are handled directly without calling LLM, saving tokens at the source.

---

### 🌐 Internationalization (i18n)

JSON-based i18n system with automatic language file discovery from `app/i18n/locales/`. `/lang` command switches UI language (Chinese/English) at runtime, also controlling LLM response language.

---

## Best Practices: Harness vs. Loop

| | Harness (Default) | Loop (`/loop <goal>`) |
|---|---|---|
| **When to use** | Rough idea, can't articulate steps, iterate as you go | Task can be decomposed into steps, each with a verifiable check |
| **Example** | "Find and fix that error on the login page" | `/loop Add pagination to the user API, run py_compile after each change` |
| **Key** | Lay out constraints upfront: goal + do-not-touch + acceptance criteria | Goals must be decomposable with verification commands |
| **Stop signal** | ESC anytime; "claimed done" ≠ actually done — run tests yourself | 3 failed corrections = insufficient info: break it down further |

---

## Command Reference

| Command | Description |
|---------|-------------|
| `/model` | Open model selection popup |
| `/lang` | Switch UI language (no-arg opens popup) |
| `/clear` | Clear conversation history |
| `/compact` | Manually compact context (--recall clears recall only) |
| `/recall` | Toggle memory recall (default on) [Ctrl+R] |
| `/memory` | Memory management (browse/search/delete cross-session memories) |
| `/loop <goal>` | Engineered task completion (plan→run→verify→self-fix) |
| `/permission` | Permission settings, auto-persisted (mode/allow/deny) |
| `/copy [-file <path>]` | Copy Markdown to clipboard or file [Ctrl+P] |
| `/ls [pattern]` | List files (fuzzy search supported) |
| `/cat <file>` | Read file content |
| `/edit` | Open system editor [Ctrl+G] |
| `/refactor [file]` | Refactor code (optimize per refactor rules) |
| `/ide` | Enter Python orchestration mode [Ctrl+I] |
| `/new` | Start new topic (mark boundary, prior history not injected) |
| `/context` | Multi-context switch (new [name] / rename [name] / <index> / del <index>) |
| `/messages` | Conversation management (multi-select/delete/merge/move) |
| `/star` | Star/unstar current conversation round |
| `/tag` | Custom #tag management (no-arg popup / add/mod/rm/ls) |
| `/help` | Show help |
| `/restart` | Restart service |
| `/quit` / `/exit` | Quit program [Ctrl+D] |

---

## Effort Levels

| Level | Thinking | Use Case |
|-------|----------|----------|
| `low` | Disabled | Quick chat, simple Q&A, fast response |
| `medium` | Disabled | General programming tasks |
| `high` | 10k tokens | Complex refactoring, architecture design |
| `max` | 20k tokens | Deep analysis, multi-step reasoning |

**Note**: Effort only controls thinking depth, **not model switching**. Switch models via `/model` — switching invalidates prompt cache, increasing token cost.

---

## Configuration Reference

Config file path:

`~/.niuma/settings.json`

<details>
<summary><strong>factories — Anthropic API configuration</strong></summary>

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

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `base_url` | `string` | — | API endpoint URL (required) |
| `api_key` | `string` | — | API key (required) |
| `options` | `string[]` | `[]` | Supported model names for this provider |

</details>

<details>
<summary><strong>llm — Model, effort, thinking budget & context management</strong></summary>

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

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `default_model` | `string` | `"claude-sonnet-4-6"` | Default model (switchable via `/model`) |
| `default_effort` | `string` | `"high"` | Default effort level — controls thinking budget |
| `max_contexts` | `integer` | `5` | Max active parallel contexts; LRU evicts oldest |
| `resume_latest_context` | `boolean` | `false` | Restore last active context on startup |
| `options` | `list` | `[]` | Model+Effort valid combinations (for /model validation) |
| `thinking_budget_low` | `string/int` | `"0"` | Thinking tokens for low effort (0 = disabled) |
| `thinking_budget_medium` | `string/int` | `"0"` | Thinking tokens for medium effort |
| `thinking_budget_high` | `string/int` | `"10000"` | Thinking tokens for high effort |
| `thinking_budget_max` | `string/int` | `"20000"` | Thinking tokens for max effort (deepest reasoning) |

</details>

<details>
<summary><strong>env — Environment variables (persona, debug, network, permissions, language)</strong></summary>

```json
{
  "env": {
    "PERSONA_NAME": "niuma",
    "MODEL_BACKGROUND": "claude-haiku-4-5",
    "LANG": "en",
    "TEMPERATURE_ZERO": "true",
    "API_TIMEOUT": "30",
    "API_ROUND_MAX": "120",
    "PERMISSION_MODE": "auto"
  }
}
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `PERSONA_NAME` | `string` | `"niuma"` | Persona name in system prompt |
| `MODEL_BACKGROUND` | `string` | `"claude-haiku-4-5"` | Background model (memory extraction, compression) |
| `LANG` | `string` | `"en"` | UI language (`zh`/`en`), also controls LLM response language |
| `TEMPERATURE_ZERO` | `string(bool)` | `"true"` | Fix temperature=0 for deterministic output |
| `API_TIMEOUT` | `string/int` | `"30"` | Streaming stall timeout (seconds) |
| `API_ROUND_MAX` | `string/int` | `"120"` | Per-turn wall-clock hard limit (seconds) |
| `PERMISSION_MODE` | `string` | `"auto"` | `auto` = whitelist+confirm / `manual` = whitelist only / `skip` = allow all |

</details>

<details>
<summary><strong>compact — Context compression settings</strong></summary>

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

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `inline_trigger` | `float` | `0.8` | Inline compression trigger ratio (blocks main loop) |
| `inline_keep_ratio` | `float` | `0.4` | Messages kept after inline compression |
| `idle_trigger` | `float` | `0.5` | Idle compression trigger (runs async after reply) |
| `idle_keep_ratio` | `float` | `0.4` | Messages kept after idle compression |
| `max_summary_tokens` | `integer` | `4096` | Max output tokens for LLM-generated summaries |
| `auto_recall_clear` | `boolean` | `false` | Auto-clear previous recall injection before each turn |

</details>

<details>
<summary><strong>memory_quality — Memory quality gating</strong></summary>

```json
{
  "memory_quality": {
    "min_recall_score": 0.35,
    "dedup_threshold": 0.3
  }
}
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `min_recall_score` | `float` | `0.35` | Recall scores below this are not injected into prompt |
| `dedup_threshold` | `float` | `0.3` | Jaccard character similarity above this = duplicate |

</details>

<details>
<summary><strong>memory_palace — Memory extraction configuration</strong></summary>

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

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `enable_v9` | `boolean` | `true` | Enable V9 perception-driven memory; `false` = legacy V8 |
| `llm_enabled` | `boolean` | `false` | Enable LLM-enhanced extraction; `false` = rule-based only |
| `base_url` | `string` | `""` | Memory LLM endpoint (required if `llm_enabled: true`) |
| `api_key` | `string` | `""` | Memory LLM API key |
| `model` | `string` | `""` | Model for memory LLM (recommend a lightweight model) |

</details>

<details>
<summary><strong>anysearch — Web search configuration</strong></summary>

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

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `api_key` | `string` | `""` | AnySearch API key |
| `base_url` | `string` | `"https://api.anysearch.com"` | API endpoint |
| `max_results` | `integer` | `5` | Maximum search results |
| `timeout` | `integer` | `15` | Search timeout (seconds) |

</details>

---

## Data Directory

```
~/.niuma/projects/{projectName}/
├── session/                 # Session data
│   ├── {sid}.json           # Conversation history
│   ├── {sid}_tasks.json     # Task state
│   ├── {sid}_trace.json     # Execution trace
│   ├── {sid}_timeline.txt   # Trace summary
│   └── input.history        # Input history (temporary, cleaned on exit)
├── memory/                  # Memory database
│   ├── memory-palace.db     # Memory storage (SQLite)
│   └── niuma_behavior.db    # Behavior log (separate, avoid lock contention)
└── ide/                     # IDE drafts
    └── {name}.py            # Orchestration script draft
```

---

## Links

- **Official Site:** [niumacode.cn](https://niumacode.cn/)
- **GitHub:** [github.com/zhiguoliu111/niuma_code](https://github.com/zhiguoliu111/niuma_code)
- **Issues:** [github.com/zhiguoliu111/niuma_code/issues](https://github.com/zhiguoliu111/niuma_code/issues)
- **Tutorials:** [niumacode.cn/tutorials](https://niumacode.cn/tutorials/)
- **Download:** [niuma.exe](https://niumacode.cn/download/niuma.exe)

---

## License

MIT License © 2026 niuma_code
