# OpenCode Token Optimization

| Category                            | Share  | Lever               |
| ----------------------------------- | ------ | ------------------- |
| System prompt, skills, MCP manifest | 30-50% | Compact AGENTS.md   |
| Tool call inputs & outputs          | 30-45% | RTK / OpenToken     |
| Stale conversation context          | 10-25% | DCP                 |
| Reasoning (extended thinking)       | 10-30% | Cap thinking budget |
| Visible model output                | 1-10%  | Caveman             |

## RTK (Rust Token Killer)

Compresses CLI command output before LLM sees it. ~89% noise removal.

Zero config after `rtk init --opencode`. MIT.

## Caveman

Behavioral compression. Injects system instructions to strip articles/filler/pleasantries/hedging from model output.

65-75% output token reduction. Six levels (lite/full/ultra/wenyan). Preserves technical accuracy.

Skills: caveman, caveman-commit, caveman-review, caveman-compress, caveman-stats, cavecrew.

## DCP (Dynamic Context Pruning)

Prunes stale tool outputs from context. Model-triggered compress + auto cleanup (de-dup, purge errors). AGPL-3.0.

`opencode plugin @tarquinen/opencode-dcp@latest --global`

## Alternatives

| Tool         | Saves              | Notes                                |
| ------------ | ------------------ | ------------------------------------ |
| OpenToken    | 50-80% tool output | 42 layers, may supersede RTK         |
| Caveman Code | ~77% claimed       | All-in-one CLI, early                |
| CodeGraph    | 61% vs raw reads   | Pre-indexed code property graph, MIT |
| Graphify     | 71.5× claimed      | Multi-modal knowledge graph, Python  |

## Recommendation

1. RTK — input tokens
2. Caveman full — output tokens
3. DCP — stale context

```bash
brew install rtk && rtk init --opencode
opencode plugin @tarquinen/opencode-dcp@latest --global
/caveman full
```

Sessions last 3-5x longer.
