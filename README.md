# how-to-choose-an-llm

An autonomous agent skill for selecting the optimal LLM based on task constraints, latency targets, economics, and deployment boundaries. Uses **Token-Oriented Object Notation (TOON)** to reduce token overhead by up to 50% compared to standard JSON.

Compatible with Claude Code, Gemini CLI, Cursor, OpenCode, and Codex.

---

## Installation

```bash
# pnpm
pnpm dlx skills add Hi9841/how-to-choose-an-llm

# bun
bunx skills add Hi9841/how-to-choose-an-llm
```

### Manual install

Copy the `how-to-choose-an-llm` folder into your agent skills path:

- **Claude Code**: `~/.claude/skills/how-to-choose-an-llm`
- **Gemini CLI / Antigravity**: `~/.gemini/config/skills/how-to-choose-an-llm`
- **Workspace**: `.agents/skills/how-to-choose-an-llm`

---

## Highlights

- **Pure in-context execution**: No Python or Node scripts required. Agents evaluate models directly.
- **TOON output**: Emits structured tables in Token-Oriented Object Notation without repeated keys or quotes.
- **Automatic first-run setup**: If no `model-inventory.toon` exists, the agent launches the setup flow on its first invocation.
- **Personalized inventories**: Each engineer rates their own available models across providers.

---

## First-run setup

Every engineer has access to different models, subscriptions, and local hardware.

When no `model-inventory.toon` file exists:
1. **Automatic detection**: The agent detects a fresh install and initiates setup immediately.
2. **Starter baseline**: Choose to load the default models or list your own active providers.
3. **1 to 10 ratings**: Score your models on Intel, Speed, and Cost (10 is cheapest or free).
4. **Local save**: Saves `model-inventory.toon` and answers your original prompt.

Reconfigure anytime by running `/how-to-choose-an-llm setup`.

---

## Archetype presets

| Archetype | Intel weight | Speed weight | Cost weight | Best for |
|---|---|---|---|---|
| `realtime` | 0.30 | 0.50 | 0.20 | Interactive UI, streaming chat, voice |
| `reasoning` | 0.70 | 0.10 | 0.20 | Bug diagnosis, math proofs, architecture |
| `bulk` | 0.20 | 0.20 | 0.60 | High-volume classification, batch jobs |
| `balanced` | 0.40 | 0.35 | 0.25 | General agent workflows, editing |
| `code` | 0.60 | 0.25 | 0.15 | Refactoring, review, feature generation |

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
  Primary pick Fable 5.1 outscores runner-up by 0.25
  Specify --full to include tags and provider details
```

---

## Repository layout

```
how-to-choose-an-llm/
├── README.md
└── how-to-choose-an-llm/
    ├── SKILL.md                       # Agent instructions and TOON protocol
    ├── model-inventory.example.toon   # Starter baseline template
    └── references/
        └── five-questions-guide.md     # Hosting, TTFT/TPOT, and eval trade-offs
```

---

## License

MIT
