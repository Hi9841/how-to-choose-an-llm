# how-to-choose-an-llm

> Agent skill for selecting the optimal large language model based on intelligence, latency, cost, context window, and deployment boundaries using Token-Oriented Object Notation (TOON).

Built for autonomous coding agents (Claude Code, Gemini CLI, Cursor, OpenCode, Codex). Follows the Agent eXperience Interface (AXI) standard for token-efficient agent interactions.

---

## Quick install

### Via agent skills CLI
```bash
# bun (recommended)
bunx skills add Hi9841/how-to-choose-an-llm

# npx
npx skills add Hi9841/how-to-choose-an-llm
```

### Manual installation
Copy the `skills/how-to-choose-an-llm` directory into your agent's skills location:
- **Claude Code**: `~/.claude/skills/how-to-choose-an-llm`
- **Gemini CLI / Antigravity**: `~/.gemini/config/skills/how-to-choose-an-llm`
- **Project workspace**: `.agents/skills/how-to-choose-an-llm`

---

## Why TOON instead of JSON?

Standard JSON payloads waste context window tokens with repeated key strings, quotes, braces, and commas.

This skill standardizes on [TOON (Token-Oriented Object Notation)](https://toonformat.dev/):
- **Tabular collections**: Declares field names once in the header (`models[N]{name,intel,speed,cost}:`) followed by compact rows.
- **30% to 50% token reduction**: Minimizes token expenditure on repetitive model evaluations and rankings.
- **Agent-first ergonomics**: Built specifically for LLM context ingestion and generation.

---

## The 5-question decision framework

Every model selection evaluates five fundamental engineering trade-offs:

1. **Open or closed model**: Self-hosted private infrastructure (vLLM, Ollama, private VPC) versus managed frontier APIs (OpenAI, Anthropic, Google).
2. **Cost dynamics**: Inverted 1 to 10 scale where 10 is cheapest or zero marginal cost and 1 is frontier reasoning expense.
3. **Latency dynamics**: Time to first token (TTFT) for interactive UIs versus throughput / time per output token (TPOT).
4. **Performance and intelligence**: Task complexity tiers from basic schema formatting (5.0) to multi-step reasoning and formal architecture (9.0+).
5. **Context window capacity**: Matching prompt and completion demands without paying unnecessary latency or cost penalties.

---

## First-run setup (`/how-to-choose-an-llm setup`)

Base model inventories differ for every engineer depending on active subscriptions, provider credits, and local hardware.

When installed or on first run:
1. Invoke `/how-to-choose-an-llm setup`.
2. Provide your active models and platforms (OpenAI, Anthropic, Google, Moonshot, DeepSeek, local Ollama).
3. Rate your models on the 1-10 scale for Intel, Speed, and Cost.
4. The skill saves your personal inventory to `model-inventory.toon`.

---

## Archetype weight presets

| Archetype | Intel ($w_{intel}$) | Speed ($w_{speed}$) | Cost ($w_{cost}$) | Use case |
|---|---|---|---|---|
| `realtime` | 0.30 | 0.50 | 0.20 | Interactive chat, autocomplete, voice assistants |
| `reasoning` | 0.70 | 0.10 | 0.20 | Hard bug fixes, architecture sketches, math proofs |
| `bulk` | 0.20 | 0.20 | 0.60 | High-volume classification, web scraping extraction |
| `balanced` | 0.40 | 0.35 | 0.25 | Standard agent execution, general assistants |
| `code` | 0.60 | 0.25 | 0.15 | Multi-file refactoring, code review, feature building |

Composite score calculation:
$$\text{Score} = (w_{intel} \times \text{intel}) + (w_{speed} \times \text{speed}) + (w_{cost} \times \text{cost})$$

---

## Example TOON response

```toon
selected:
  name: Fable 5.1
  score: 7.9
  intel: 9.0
  speed: 6.0
  cost: 5.0
  context: 256000
  open: false
cost_saver:
  name: Gemini 3.8 Flash
  cost: 9.0
  intel: 6.0
escalation:
  name: Fable 5.1
  intel: 9.0
ranked[6]{rank,name,score,intel,speed,cost}:
  1,Fable 5.1,7.9,9.0,6.0,5.0
  2,GPT 6 Astra,7.65,8.5,7.0,5.0
  3,Opus 5,7.35,7.5,7.0,7.0
  4,Kimi K3,7.1,8.0,3.0,6.0
  5,GPT 5.6 Sol,7.0,7.0,7.0,7.0
  6,Gemini 3.8 Flash,6.8,6.0,8.0,9.0
help[2]:
  Specify `--full` if you want provider, tags, and context bounds expanded
  Primary pick Fable 5.1 outscores runner-up by 0.25
```

---

## Repository layout

```
how-to-choose-an-llm/
├── README.md                          # Repository overview and setup guide
└── skills/
    └── how-to-choose-an-llm/          # The main skill folder
        ├── SKILL.md                   # Agent instructions, decision protocol, and TOON schemas
        ├── model-inventory.toon       # Engineer model database
        └── references/
            └── five-questions-guide.md # Technical reference on hosting, TTFT/TPOT, and evals
```

---

## License

MIT
