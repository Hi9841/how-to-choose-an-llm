---
name: choose-llm
description: Select the optimal large language model for a task based on intelligence, latency, cost, context window, and open versus closed hosting constraints. Includes first-run setup for personal engineer model inventories.
---

# Choose LLM

Select the best model for any AI task by balancing intelligence, speed, cost, context size, and deployment boundaries against an engineer's personal model inventory.

This skill is agent-first, self-contained, and portable. It requires no external scripts or runtime environments. Any AI agent executes the protocol directly and outputs results in Token-Oriented Object Notation (TOON) for token efficiency.

## Setup workflow (`/how-to-choose-an-llm setup`)

The base model inventory differs for every engineer depending on active subscriptions, enterprise accounts, API access, and local GPU hardware.

### When to run setup
Run the setup workflow when:
1. The user explicitly requests `/how-to-choose-an-llm setup` or asks to configure models.
2. On first run when `model-inventory.toon` is uninitialized or missing.

### Setup protocol
Guide the engineer through four quick steps:

1. **List active models**: Ask which models the engineer actively uses across their providers (OpenAI, Anthropic, Google, Moonshot, DeepSeek, Groq, local Ollama/vLLM).
2. **Assign 1-10 ratings**:
   - **Intel (1-10)**: Task accuracy, reasoning depth, and instruction following.
   - **Speed (1-10)**: Responsiveness based on TTFT and TPOT.
   - **Cost (1-10)**: Economic efficiency (10 is free or cheapest, 1 is most expensive).
   - **Context**: Maximum usable token window.
   - **Open**: `true` for self-hosted or weights-available models, `false` for proprietary APIs.
   - **Provider & tags**: Hosting vendor and task tags (for example: `"coding,reasoning,fast"`).
3. **Persist inventory**: Save the confirmed models to `model-inventory.toon` in the skill folder using TOON tabular syntax:
   ```toon
   models[N]{name,intel,speed,cost,context,open,provider,tags}:
     Model A,8.0,7.0,6.0,128000,false,Provider,"tag1,tag2"
     Model B,9.0,5.0,4.0,256000,false,Provider,"reasoning,coding"
   ```
4. **Confirm in TOON**: Output the saved table and present next action commands.

---

## Rating scale

All metrics use a 1 to 10 scale where higher is always better:
- **Intel (1-10)**: Task accuracy, reasoning depth, and instruction following.
- **Speed (1-10)**: Responsiveness based on TTFT (time to first token) and TPOT (time per output token).
- **Cost (1-10)**: Economic efficiency. 10 is cheapest or zero marginal cost. 1 is highest expense.

---

## Decision protocol

When asked to select, recommend, or route to an LLM, perform this four-step sequence directly:

### 1. Identify task constraints

Determine the five core task parameters:
1. **Context size**: Estimated input plus completion token count.
2. **Deployment boundary**: Does the task require private or on-prem hosting (open model only), or can it use third-party APIs?
3. **Minimum intelligence floor**:
   - `5.0 - 6.5`: Text extraction, formatting, simple classification.
   - `7.0 - 8.0`: Standard agent workflows, multi-file code generation, conversational chat.
   - `8.5 - 10.0`: Architecture design, deep reasoning, competitive coding, formal logic.
4. **Latency sensitivity**:
   - High: Realtime user interfaces, streaming text, interactive chat.
   - Moderate: Tool-calling loops where each step waits for model response.
   - Low: Offline batch processing, background audits, overnight jobs.
5. **Cost sensitivity**:
   - High volume: Processing thousands of documents or continuous polling.
   - Balanced: Standard production application features.
   - Low volume: One-off mission-critical tasks where quality outranks cost.

### 2. Locate model inventory

1. If the engineer provides a model list or ratings directly in the conversation, use those models and scores.
2. If `model-inventory.toon` exists in the skill folder or workspace, read it.
3. If no inventory exists, prompt the engineer to run `/how-to-choose-an-llm setup`.

### 3. Filter and calculate composite scores

#### Hard filter gates
Eliminate non-viable models before ranking:
- Discard models whose `context` is smaller than required context.
- Discard closed models if the task specifies open or self-hosted deployment.
- Discard models whose `intel` rating is below the minimum intelligence floor.

#### Archetype weight presets
Select the matching task archetype:

| Archetype | Intel weight ($w_{intel}$) | Speed weight ($w_{speed}$) | Cost weight ($w_{cost}$) | Typical use cases |
|---|---|---|---|---|
| `realtime` | 0.30 | 0.50 | 0.20 | Interactive chat, autocomplete, voice assistants |
| `reasoning` | 0.70 | 0.10 | 0.20 | Hard bug fixes, architecture sketches, math proofs |
| `bulk` | 0.20 | 0.20 | 0.60 | Batch data classification, web scraping extraction |
| `balanced` | 0.40 | 0.35 | 0.25 | Standard agent execution, general assistants |
| `code` | 0.60 | 0.25 | 0.15 | Multi-file refactoring, code review, feature building |

Compute the composite score for each eligible model:
$$\text{Score} = (w_{intel} \times \text{intel}) + (w_{speed} \times \text{speed}) + (w_{cost} \times \text{cost})$$

Sort eligible models descending by composite score. Break ties using higher `intel`, then higher `speed`.

### 4. Output response in TOON format

Follow Agent eXperience Interface (AXI) standards: emit structured TOON output to save context tokens.

#### Standard recommendation format
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

#### Definitive empty state
When zero models satisfy the constraints, state the zero explicitly with the blocking constraint:
```toon
models: 0 models found matching constraints (min_intel=9.5)
help[2]:
  Review available inventory in model-inventory.toon
  Run `/how-to-choose-an-llm setup` to add higher intelligence models
```

---

## Reference guide

Read `references/five-questions-guide.md` for background on hosting economics, TTFT versus TPOT, benchmark contamination, and context window retrieval trade-offs.
