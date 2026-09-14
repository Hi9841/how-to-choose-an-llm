# how-to-choose-an-llm

Agent skill to pick the right LLM for any task. Uses **TOON (Token-Oriented Object Notation)** to cut prompt overhead by up to 50%.

Zero scripts. Zero dependencies. Runs 100% in-context.

---

## Install

### pnpm
```bash
pnpm dlx skills add Hi9841/how-to-choose-an-llm
```

### bun
```bash
bunx skills add Hi9841/how-to-choose-an-llm
```

### Manual
Copy `how-to-choose-an-llm` into your agent skills directory:
- Claude Code: `~/.claude/skills/how-to-choose-an-llm`
- Gemini CLI: `~/.gemini/config/skills/how-to-choose-an-llm`
- Workspace: `.agents/skills/how-to-choose-an-llm`

---

## How it works

1. **Auto setup on first run**: If no model inventory exists, the agent starts setup on your first prompt.
2. **Evaluates 5 constraints**: Checks open vs closed, speed, intelligence floor, cost, and context size.
3. **Picks the winner**: Scores models against your task archetype and outputs the top choice in TOON.

---

## Archetypes

| Archetype | Intel | Speed | Cost | Best for |
|---|---|---|---|---|
| `realtime` | 30% | 50% | 20% | Interactive UI, streaming chat, voice |
| `reasoning` | 70% | 10% | 20% | Bug diagnosis, architecture, math |
| `bulk` | 20% | 20% | 60% | Scraping, batch classification, data pipelines |
| `balanced` | 40% | 35% | 25% | General coding, multi-file edits, assistants |
| `code` | 60% | 25% | 15% | Code review, complex refactoring, test generation |

---

## Example output

```toon
selected:
  name: Fable 5.1
  score: 7.9
  intel: 9.0
  speed: 6.0
  cost: 5.0
  context: 256000
cost_saver:
  name: Gemini 3.8 Flash
  cost: 9.0
escalation:
  name: Fable 5.1
  intel: 9.0
ranked[3]{rank,name,score}:
  1,Fable 5.1,7.9
  2,GPT 6 Astra,7.65
  3,Opus 5,7.35
```

---

## First-run setup

When no `model-inventory.toon` exists:
1. Agent asks if you want to load the starter baseline (`model-inventory.example.toon`) or enter your own models.
2. Rates models on a 1-10 scale for Intel, Speed, and Cost (10 = cheapest/free).
3. Saves `model-inventory.toon` locally and finishes your query.

Re-run anytime with `/how-to-choose-an-llm setup`.

---

## Repo structure

```
how-to-choose-an-llm/
├── README.md
└── how-to-choose-an-llm/
    ├── SKILL.md                       # Agent instructions & TOON protocol
    ├── model-inventory.example.toon   # Starter baseline template
    └── references/
        └── five-questions-guide.md     # Hosting, TTFT/TPOT, and eval trade-offs
```

---

## License

MIT
