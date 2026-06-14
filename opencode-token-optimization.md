# OpenCode Token Optimization: RTK, Caveman, DCP & Alternatives

Research findings on tools that reduce token consumption and speed up AI coding agent responses.

---

## Overview

Token consumption in OpenCode splits across five areas:

| Category                            | Typical share | Lever               |
| ----------------------------------- | ------------- | ------------------- |
| System prompt, skills, MCP manifest | 30-50%        | Compact AGENTS.md   |
| Tool call inputs & outputs          | 30-45%        | RTK / OpenToken     |
| Stale conversation context          | 10-25%        | DCP                 |
| Reasoning (extended thinking)       | 10-30%        | Cap thinking budget |
| Visible model output                | 1-10%         | Caveman             |

Three complementary tools attack the biggest levers: **RTK** (input/command tokens), **Caveman** (output/response tokens), and **DCP** (context/pruning).

---

## RTK (Rust Token Killer)

Compresses CLI command output before it reaches the LLM context window.

### How it works

Intercepts stdout/stderr from shell commands (`cargo test`, `git diff`, `grep`, `ls`, etc.), strips verbose boilerplate, preserves errors/stack traces/diffs. Acts as transparent proxy between shell and model.

### Benchmarks

- `cargo test`: 92% token reduction
- `git status`: 81%
- `find`: 78%
- `grep`: 50%
- Overall average: ~89% noise removal
- Real user: 15,720 commands, 138M tokens saved

### Pros

- Zero config after `rtk init --opencode`
- Works with Claude Code, Cursor, Gemini CLI, Aider, Codex, Windsurf, Cline, Copilot
- Single Rust binary, no deps
- Sessions last ~3x longer on capped plans
- Free, MIT

### Cons

- Aggressive compression could theoretically hide subtle warnings (though errors preserved)

### Installation

```bash
brew install rtk                           # macOS
curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh
cargo install --git https://github.com/rtk-ai/rtk
rtk init --opencode                        # auto-installs global plugin hook
```

---

## Caveman

Behavioral compression framework that makes the LLM speak in terse caveman style.

### How it works

Injects system instructions that strip articles, filler, pleasantries, hedging from every model response. Code blocks, symbols, error strings, API names preserved exactly.

### Levels

| Level             | Token savings | Style                                               |
| ----------------- | ------------- | --------------------------------------------------- |
| lite              | ~30%          | Drop filler, full sentences                         |
| full (default)    | ~65%          | Drop articles, fragments OK                         |
| ultra             | ~75%          | Bare fragments, abbreviations, arrows for causality |
| wenyan-lite/ultra | varies        | Classical Chinese compression                       |

### Skills included

| Skill            | Purpose                                        |
| ---------------- | ---------------------------------------------- |
| caveman          | Core terse mode                                |
| caveman-commit   | Conventional Commits, ≤50 chars                |
| caveman-review   | One-line PR findings                           |
| caveman-compress | Compress .md memory files                      |
| caveman-stats    | Session token receipts                         |
| cavecrew         | Subagent delegation (60% smaller tool results) |

### Pros

- 65-75% output token reduction
- Preserves full technical accuracy
- Six intensity levels
- Persistent mode, no drift
- Auto-clarity for security warnings

### Cons

- Only affects **output tokens** (model responses), not input/command tokens
- Feels unnatural for conversational tasks
- Wenyan modes require Chinese literacy
- Plugin has some complexity (~230 lines JS + config)

### Installation

```bash
# Via npm plugin (simplest)
opencode plugin opencode-caveman --global

# Or manual config (copy files to ~/.config/opencode/)
# See: https://github.com/JuliusBrussee/caveman
```

Toggle with `/caveman [lite|full|ultra]`. Disable with `stop caveman`.

---

## RTK + Caveman + DCP = Complementary Stack

They target different layers — use all three:

| Layer          | Tool    | What it saves                      | Reduction                |
| -------------- | ------- | ---------------------------------- | ------------------------ |
| Command output | RTK     | Input tokens from CLI noise        | 60-90%                   |
| Stale context  | DCP     | Obsolete tool outputs from context | Variable, prevents bloat |
| Response prose | Caveman | Output tokens from model           | 40-75%                   |

**Combined effect**: Sessions last 3-5x longer on same token budget, context stays focused.

**Quick pick**: Use **RTK** for input compression, **DCP** for session hygiene, **Caveman** for terse output.

---

## Alternatives Found

### OpenToken (MrGray17) ⭐ Promising

Full compression engine combining ideas from RTK, DCP, SlimEdit. 42 compression layers, 35 stages, 10 command families. 431 tests.

- **Saves**: 50-80% on tool output tokens
- **Works with**: OpenCode, Cursor, Windsurf, Claude Desktop, VS Code Copilot
- **Runtime**: Bun/TypeScript (no build step)
- **Install**: `opencode plugin @mrgray17/opentoken --global` (auto-loads, zero config)
- **Stars**: 129 ⭐ (growing fast)
- **Key features**: Secrets redaction, progressive disclosure, reversible compression, auto-tuning, cross-call dedup, stats dashboard; pipeline routing per command family; safety filters prevent regression
- **0-risk principle**: every stage ends with conservative filter — output only shrinks or stays
- **Pros**: More comprehensive than RTK alone; pipeline routing per command family; safety filters prevent regression
- **Cons**: Newer project, less battle-tested; more complex setup
- **Verdict**: Most comprehensive single-tool option. Worth watching — may supersede RTK for input compression.

### DCP (Dynamic Context Pruning)

Prunes stale tool outputs from conversation context before sending to LLM. Developed by tarquinen, 3.3k+ stars.

### How it works

DCP reduces context size through a compress tool + automatic cleanup strategies. Session history never modified — pruned content replaced with placeholders before requests reach LLM.

**Compress tool**: Model-exposed tool that replaces closed, stale conversation content with high-fidelity technical summaries. Smarter than OpenCode's built-in compaction — triggers on task completion instead of at max context, compresses only specific messages.

Two compression modes:

- `range` — Compresses contiguous spans into one or more summaries. Overlapping compressions nest earlier summaries inside new ones.
- `message` (experimental) — Compresses individual raw messages independently for surgical context management.

**Deduplication**: Identifies repeated tool calls (same tool, same args), keeps only most recent output. Recalculated when compress runs.

**Purge Errors**: Prunes inputs from errored tool calls after N turns (default: 4). Error messages preserved, only large input content removed.

### Strategies

| Strategy       | Default                        | What it does                                |
| -------------- | ------------------------------ | ------------------------------------------- |
| compress       | mode: range, permission: allow | Model-triggered summary replacement         |
| deduplication  | enabled                        | Keep latest output of repeated tool calls   |
| purgeErrors    | enabled, turns: 4              | Remove inputs of consistently failing tools |
| turnProtection | disabled, 4 turns              | Protect recent tool outputs from pruning    |

### Commands

| Command                 | Description                                         |
| ----------------------- | --------------------------------------------------- |
| `/dcp`                  | Show available commands                             |
| `/dcp context`          | Session token usage breakdown + savings             |
| `/dcp stats`            | Cumulative pruning stats across sessions            |
| `/dcp sweep [N]`        | Prune all tools since last user message (or last N) |
| `/dcp manual [on\|off]` | Toggle manual mode (AI won't auto-prune)            |
| `/dcp compress [focus]` | Trigger single compress execution                   |
| `/dcp decompress <n>`   | Restore compression by ID                           |
| `/dcp recompress <n>`   | Re-apply user-decompressed compression              |

### Configuration

Config files searched hierarchically: `~/.config/opencode/dcp.jsonc` → `$OPENCODE_CONFIG_DIR/dcp.jsonc` → `.opencode/dcp.jsonc`. Each level overrides previous. Key settings: `compress.mode`, `compress.maxContextLimit`, `compress.nudgeFrequency`, `strategies.deduplication`, `strategies.purgeErrors.turns`.

### Impact on Prompt Caching

Pruning changes messages, invalidating cached prefixes from that point. Trade-off: ~85% cache hit rate with DCP vs ~90% without. Token savings + reduced context poisoning usually outweigh cache misses, especially in long sessions. No impact for request-based billing (GitHub Copilot) or uniform token pricing (Cerebras).

### Protected Tools

Always protected: `task`, `skill`, `todowrite`, `todoread`, `compress`, `batch`, `plan_enter`, `plan_exit`, `write`, `edit`. Customizable via `protectedTools` arrays in commands/strategies config.

### Pros

- Model-aware compression (smarter than static compaction)
- Zero-config auto-cleanup (dedup, purgeErrors) with no LLM cost
- Session history preserved (placeholders only)
- slash commands for manual control
- Hierarchical config (global → project)
- Prompt overrides for advanced users

### Cons

- AGPL-3.0-or-later license (restrictive for commercial)
- Invalidates prompt cache (~5% hit rate reduction)
- Experimental message mode may be unstable
- Compress quality depends on model capability
- Config complexity for fine-tuning

### Installation

```bash
opencode plugin @tarquinen/opencode-dcp@latest --global
```

Restart OpenCode after install. Config auto-generated on first run.

### Headroom

Reversible compression with CCR, multi-agent shared memory.

- **Pros**: Reversible decompression, shared memory across agents
- **Cons**: More niche, less OpenCode-specific

### Caveman Code (JuliusBrussee)

Full CLI bundling 4 compression layers (prompt, RTK, output, context).

- **Saves**: ~77% combined (claimed)
- **Install**: `npm install -g caveman-code`
- **Verdict**: All-in-one solution; still early

### CodeGraph (colbymchenry)

Pre-indexed code knowledge graph for AI coding assistants. Uses tree-sitter to build a Code Property Graph (CPG) — functions, classes, calls, dependencies as queryable nodes/edges. Reduces tokens by replacing raw file reads with scoped graph queries.

- **Saves**: 61% fewer tokens vs raw file reads (benchmarked)
- **Install**: `npx @colbymchenry/codegraph` (interactive installer)
- **OpenCode plugin**: `opencode-codegraph` (in `plugin` array)
- **MCP**: `codegraph serve --mcp` (auto-configured by installer)
- **Tools**: `codegraph_explore`, `codegraph_callers`, `codegraph_callees`, `codegraph_impact`, `codegraph_search`
- **Platforms**: OpenCode, Claude Code, Cursor, Codex CLI, Gemini CLI, Hermes Agent, Kiro
- **Stars**: 3.3k+ ⭐
- **License**: MIT
- **Pros**: Auto-detects AI agents and wires MCP config; 100% local, no network calls; tree-sitter based, no LLM cost for indexing; incremental updates
- **Cons**: Only covers structured code (no docs, images, multi-modal); indexing takes time on large repos; graph quality depends on parsing coverage
- **Verdict**: Strong choice for code-only token reduction via structured queries

### Graphify (safishamsi)

Multi-modal knowledge graph builder. Combines tree-sitter AST parsing with LLM-driven semantic extraction to build a graph from source code, docs, papers, and diagrams. Ships as Python CLI + MCP server.

- **Saves**: 71.5× token reduction claimed (1.7k tokens vs 123k naive per query)
- **Install**: `uv tool install graphifyy`
- **OpenCode**: `graphify install --platform opencode` (writes AGENTS.md + plugin)
- **Commands**: `/graphify`, `/graphify query`, `/graphify path`, `/graphify explain`
- **Platforms**: Claude Code, OpenCode, Codex, Cursor, Gemini CLI, Copilot CLI, Aider, OpenClaw, Trae, Kiro, more
- **Stars**: Growing fast
- **License**: MIT
- **Pros**: Multi-modal (code + docs + images + diagrams); 71.5× token compression; confidence tags on relationships (EXTRACTED/INFERRED/AMBIGUOUS); interactive HTML visualization; MCP server
- **Cons**: Python/`uv` dependency; LLM cost on initial extraction pass (pass 3); overhead for small repos (<6 files); still early, plugin integration evolving
- **Verdict**: Best compression ratio for mixed corpora; ideal for onboarding to large unfamiliar codebases

---

## Recommendation

**Current best stack**:

1. **RTK** — compress input tokens, works across all tools
2. **Caveman full** — compress output tokens
3. **DCP** — prune stale context in long sessions

**Watchlist**:

- **OpenToken** — may supersede RTK; 46+ stages, cross-platform
- **Caveman Code** — all-in-one CLI bundling 4 compression layers
- **CodeGraph** — pre-indexed CPG (code property graph) for AI agents
- **Graphify** — multi-modal knowledge graph, 71.5× token reduction claimed

**Not recommended yet**:

- Headroom (niche, less active)

---

## Quick Start

```bash
# 1. Install RTK
brew install rtk && rtk init --opencode

# 2. Install DCP
opencode plugin @tarquinen/opencode-dcp@latest --global

# 3. Enable Caveman (in OpenCode TUI)
/caveman full

# 4. Verify
rtk gain       # should show active
/dcp stats     # shows pruning stats
/caveman-stats # shows token savings
```

Session should now last 3-5x longer on same budget. Commands produce compact output. Context stays lean. Responses are terse but accurate.
