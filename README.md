# [niuma_code](https://niumacode.cn/)

> A companion tool for Claude Code — **subtract context, multiply attention**. Every LLM token lands exactly where it matters.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-lightgrey.svg)]()
[![Version: 0.2.0](https://img.shields.io/badge/Version-0.2.0--alpha-green.svg)]()

---

## What is niuma_code?

niuma_code is a **Python-based terminal tool** for Claude Code users who want tighter control over context and attention. Packaged as a single portable `niuma.exe` — download, configure, run. No installation.

Models don't lack attention mechanisms — they lack placing attention where it matters. The longer the context, the more noise dilutes truly critical information. **niuma_code does one thing: subtract context, multiply attention.**

---

## Quick Start

### 1. Download

Download [`niuma.exe`](https://niumacode.oss-cn-beijing.aliyuncs.com/releases/niuma.exe) — no install needed.

### 2. Configure

Create `~/.niuma/settings.json`:

```json
{
  "factories": [
    {
      "base_url": "https://api.anthropic.com",
      "api_key": "your-api-key"
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

### ⚙️ Autonomous Harness Mode (Default)

The default mode — no commands needed. LLM explores autonomously in a tool loop, thinking and acting simultaneously until the task is done.

- LLM decides when to read files, edit code, run commands — autonomously
- API error auto-retry, streaming render, ESC cancel, auto-compression for long outputs
- Best for fuzzy, hard-to-decompose tasks where LLM judges when it's done

---

### 🔁 Loop Engineered Orchestration

`/loop <goal>` enters a goal-driven self-loop: plan with verification commands, execute sequentially, verify each step, self-correct on failure (3-strike mechanism).

- **Plan → Execute → Verify → Failure retry** with failure reason back-feed
- Dual gates: `MAX_ROUNDS=20` task cap + `MAX_RETRIES=3` self-correction
- Ideal for decomposable, verifiable engineering tasks

---

### 🖥️ TUI Full-Screen Mode

Based on `prompt_toolkit` full-screen UI. Input fixed at the bottom, append new messages anytime during LLM streaming — no content gets pushed away.

- 5 modal overlays: model settings, message queue, context switch, conversation management, permission confirmation
- Mouse drag selection, scroll browsing, left-click auto-copy, `Ctrl+Scroll` font zoom
- Real-time status bar: thinking preview, input/output token counts, compression progress

---

### 💻 IDE Orchestration Mode

Full-screen code editor. Write orchestration scripts in native Python syntax, embedding LLM calls into controllable workflows.

- Inject `llm_call` / `llm_confirm` / `llm_judge` functions
- `F5` preview (AST static analysis), `F6` unattended execution (auto-approve tool permissions)
- Secure sandbox + dangerous import blacklist (`os`/`subprocess`/`socket` etc.)

---

### 🔌 Multi-Provider Routing

Configure multiple API endpoints in `settings.json` — requests auto-route to the correct provider by model name.

- Each provider declares `base_url` / `api_key` / supported model list
- `#tag` or `/model` to switch models — requests auto-hit the correct endpoint
- Three-layer config cascade (user-level / project-level / project-local), later overrides earlier

---

### 🗂️ Parallel Context Management

Expand a single conversation into N parallel contexts, each independently maintaining history, token counts, and compression state — zero cross-contamination.

- LRU eviction, default cap 5 (`max_contexts` configurable) — evicted = archived & restorable
- `/context` visual overlay for switching; async summary generation on exit
- `/messages` multi-select by unit — delete / reorder / LLM summary merge

---

### 🕸️ Code Knowledge Graph

Built on `tree-sitter` to parse code structure, constructing symbol definitions, call chains, and dependency graphs — LLM locates precisely before reading lines.

- 4 read-only query tools: locate symbols, file dependencies, references, call chains
- Returns `file:line` and signatures — replaces grep's needle-in-a-haystack
- Three-factor decision for on-demand rebuild, avoiding full parse on every launch

---

### 🤖 Parallel Sub-Agent Research

Read-only sub-agents execute tools in parallel, researching in isolated contexts and returning only summaries — main context stays clean.

- Multiple sub-agents run read-only tools in parallel, non-blocking
- Isolated context research, summaries only — no main conversation pollution
- Called on demand within harness mode (not a standalone mode)

---

### 🧠 Perception-Driven Memory

Persistent memory powered by [memory-palace](https://github.com/zhiguoliu111/memory-palace) — runtime events are captured in real-time as memories, accumulating experience across sessions.

- 10 perception event types (eye / body / tongue / nose / outcome etc.), written in real-time
- Fact triples + conversation summaries, 4-layer retrieval + Bayesian decay
- Auto-injected into system prompt on next session, stable recall position

---

### 🎯 Outcome Reward Tracking

Tracks the full lifecycle of task objectives, computes reward scores by completion quality, and writes them as perception events into memory.

- `OutcomeTracker` computes 0.0–1.0 reward scores across the full task lifecycle
- Reward scores become reuse-value evaluation signals in memory
- One of the 10 perception event types, integrated with read/write/tool-call events

---

## Best Practices: Harness vs. Loop

|                 | Harness (Default)                                            | Loop (`/loop <goal>`)                                        |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **When to use** | Rough idea, can't articulate steps, iterate as you go        | Task can be decomposed into steps, each with a verifiable check |
| **Example**     | "Find and fix that error on the login page"                  | `/loop Add pagination to the user API, run py_compile after each change` |
| **Key**         | Lay out constraints upfront: goal + do-not-touch + acceptance criteria | Goals must be decomposable with verification commands        |
| **Stop signal** | ESC anytime; "claimed done" ≠ actually done — run tests yourself | 3 failed corrections = insufficient info: break it down further |

---

## Command Reference

| Command                | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `/ide`                 | Enter full-screen code editor                                |
| `/context`             | Multi-context management (new / rename / switch / del)       |
| `/messages`            | Conversation management (multi-select / delete / merge / move) |
| `/model`               | View / switch model                                          |
| `/copy [-file <path>]` | Copy Markdown to clipboard or file                           |
| `/help`                | Show help                                                    |
| `/resume`              | Resume unfinished tasks                                      |
| `/clear`               | Clear conversation history                                   |
| `/restart`             | Restart niuma                                                |
| `/quit` / `/exit`      | Exit program                                                 |

---

## Configuration Reference

Config file: `~/.niuma/settings.json`

<details>
<summary><strong>factories — Multi-provider API configuration</strong></summary>


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

| Parameter  | Type       | Default | Description                             |
| ---------- | ---------- | ------- | --------------------------------------- |
| `base_url` | `string`   | —       | API endpoint URL (required)             |
| `api_key`  | `string`   | —       | API key (required)                      |
| `options`  | `string[]` | `[]`    | Supported model names for this provider |

</details>

<details>
<summary><strong>llm — Model, effort, thinking budget & context management</strong></summary>


```json
{
  "llm": {
    "default_model": "claude-sonnet-4-6",
    "default_effort": "high",
    "max_contexts": 5,
    "thinking_budget_high": "10000",
    "thinking_budget_max": "20000"
  }
}
```

| Parameter                | Type         | Default               | Description                                        |
| ------------------------ | ------------ | --------------------- | -------------------------------------------------- |
| `default_model`          | `string`     | `"claude-sonnet-4-6"` | Default model (switchable via `/model`)            |
| `default_effort`         | `string`     | `"high"`              | Default effort level — controls thinking budget    |
| `max_contexts`           | `integer`    | `5`                   | Max active parallel contexts; LRU evicts oldest    |
| `thinking_budget_low`    | `string/int` | `"0"`                 | Thinking tokens for low effort (0 = disabled)      |
| `thinking_budget_medium` | `string/int` | `"0"`                 | Thinking tokens for medium effort                  |
| `thinking_budget_high`   | `string/int` | `"10000"`             | Thinking tokens for high effort                    |
| `thinking_budget_max`    | `string/int` | `"20000"`             | Thinking tokens for max effort (deepest reasoning) |

</details>

<details>
<summary><strong>env — Environment variables (persona, debug, network, permissions)</strong></summary>


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

| Parameter          | Type           | Default              | Description                                                  |
| ------------------ | -------------- | -------------------- | ------------------------------------------------------------ |
| `PERSONA_NAME`     | `string`       | `"niuma"`            | Persona name in system prompt                                |
| `MODEL_BACKGROUND` | `string`       | `"claude-haiku-4-5"` | Background model (memory extraction, compression)            |
| `LANG`             | `string`       | `"en"`               | UI language (`zh`/`en`), also controls LLM response language |
| `TEMPERATURE_ZERO` | `string(bool)` | `"true"`             | Fix temperature=0 for deterministic output                   |
| `API_TIMEOUT`      | `string/int`   | `"30"`               | Streaming stall timeout (seconds)                            |
| `API_ROUND_MAX`    | `string/int`   | `"120"`              | Per-turn wall-clock hard limit (seconds)                     |
| `PERMISSION_MODE`  | `string`       | `"auto"`             | `auto` = whitelist+confirm / `manual` = whitelist only / `skip` = allow all |

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
    "max_summary_tokens": 4096
  }
}
```

| Parameter            | Type      | Default | Description                                         |
| -------------------- | --------- | ------- | --------------------------------------------------- |
| `inline_trigger`     | `float`   | `0.8`   | Inline compression trigger ratio (blocks main loop) |
| `inline_keep_ratio`  | `float`   | `0.4`   | Messages kept after inline compression              |
| `idle_trigger`       | `float`   | `0.5`   | Idle compression trigger (runs async after reply)   |
| `idle_keep_ratio`    | `float`   | `0.4`   | Messages kept after idle compression                |
| `max_summary_tokens` | `integer` | `4096`  | Max output tokens for LLM-generated summaries       |

</details>

<details>
<summary><strong>memory_palace — Memory extraction configuration</strong></summary>


```json
{
  "memory_palace": {
    "enable_v9": true,
    "llm_enabled": false
  }
}
```

| Parameter     | Type      | Default | Description                                               |
| ------------- | --------- | ------- | --------------------------------------------------------- |
| `enable_v9`   | `boolean` | `true`  | Enable V9 perception-driven memory; `false` = legacy V8   |
| `llm_enabled` | `boolean` | `false` | Enable LLM-enhanced extraction; `false` = rule-based only |
| `base_url`    | `string`  | `""`    | Memory LLM endpoint (required if `llm_enabled: true`)     |
| `api_key`     | `string`  | `""`    | Memory LLM API key                                        |
| `model`       | `string`  | `""`    | Model for memory LLM (recommend a lightweight model)      |

</details>

---

## Links

- **Official Site:** [niumacode.cn](https://niumacode.cn/)
- **GitHub:** [github.com/zhiguoliu111/niuma_code](https://github.com/zhiguoliu111/niuma_code)
- **Issues:** [github.com/zhiguoliu111/niuma_code/issues](https://github.com/zhiguoliu111/niuma_code/issues)
- **Download:** [niuma.exe](https://niumacode.oss-cn-beijing.aliyuncs.com/releases/niuma.exe)

---

## License

MIT License © 2026 niuma_code
