# how-to-choose-an-llm

An instruction-only skill that ranks your available model deployments by task constraints and personal ratings. Recommendations use **TOON (Token-Oriented Object Notation)**.

Zero scripts. Zero dependencies. Runs 100% in-context.

The discovered skill name is `choose-llm`; the repository is named `how-to-choose-an-llm`. Use an agent that supports skills, or load its instructions manually. Invocation syntax varies by agent.

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

### npx
```bash
npx skills add Hi9841/how-to-choose-an-llm
```

---

## Manual install

Copy the inner `how-to-choose-an-llm/` folder into your agent's configured skills directory as `choose-llm/`. Keep `SKILL.md`, the example inventory, and `references/` together. Do not copy the outer repository as a nested skill.

Ask: "Use choose-llm to pick a model for my task." Where supported, invoke `$choose-llm` or `/choose-llm`. Ask for "choose-llm setup" to configure your inventory. The old repository-name wording is recognized in conversation, not guaranteed to be a registered command.

---

## How it works

1. Use an in-prompt inventory, an explicitly named file, a workspace inventory, or a legacy skill-folder inventory, in that order. Start setup only when none is available.
2. Filter by usable context, intelligence floor, permitted hosting, and any separate open-weight requirement.
3. Rank eligible deployments by the task's intelligence, speed, and cost weights. Apply the same filters to cheaper alternatives and escalation choices.

### Hosting is not weight availability

`open` describes whether weights are available. `hosting` describes where this particular deployment runs:

| Hosting | Meaning | Allowed for local-only tasks? |
|---|---|---|
| `local` | Own machine or on-prem infrastructure | Yes |
| `private-cloud` | User-controlled cloud deployment | No |
| `provider-api` | Third-party inference service, including open weights | No |
| `unknown` | Deployment not confirmed | No |

For a broader private-hosting request, clarify whether controlled cloud is allowed. Older inventories without `hosting` remain readable, but the missing field is treated as `unknown`. Confirm it before selecting a deployment for restricted data. Open weights alone do not establish privacy, compliance, or hardware availability.

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

Illustrative starter ratings, reasoning weights, 128000 tokens, intelligence floor 8.5, provider APIs allowed. These are not verified current model specifications.

```toon
archetype: reasoning
verification: unverified example
selected:
  name: Fable 5.1
  score: 7.9
  intel: 9.0
  speed: 6.0
  cost: 5.0
  context: 256000
  hosting: provider-api
ranked[2]{rank,name,score}:
  1,Fable 5.1,7.9
  2,GPT 6 Astra,7.65
```

---

## First-run setup

When neither a supplied nor a saved inventory is available:

1. The agent asks whether to adapt the illustrative starter or enter your own deployments.
2. Confirm model access, hosting, context, and ratings for intelligence, speed, and cost efficiency on a 1-10 scale. A cost rating of 10 means cheapest.
3. The agent saves the confirmed inventory in your workspace and fulfills the original request. Updating an explicitly chosen existing file preserves that location.

The starter is an example, not a live catalog. Ratings are subjective; they are not benchmark results. For real recommendations, the agent checks changing provider facts against official sources when tools allow. Without verification, the recommendation is labeled provisional. No provider access or live model benchmark is performed by the skill itself.

Reconfigure with "choose-llm setup". Keep personal inventory files out of public commits unless intentionally shared.

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
