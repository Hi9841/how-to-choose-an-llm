---
name: choose-llm
description: Select the optimal large language model for a task based on intelligence, latency, cost, context window, and open versus closed hosting constraints. Includes first-run setup for personal engineer model inventories.
---

# Choose LLM

Select the best model for any AI task by balancing intelligence, speed, cost, context size, and deployment boundaries against an engineer's personal model inventory.

This skill is agent-first, self-contained, and portable. It requires no external scripts or runtime environments. Any AI agent executes the protocol directly and outputs results in Token-Oriented Object Notation (TOON) for token efficiency.

## Setup workflow (`choose-llm setup`)

The base model inventory differs for every engineer depending on active subscriptions, enterprise accounts, API access, and local GPU hardware.

### When to run setup
The setup workflow executes in two scenarios:
1. **Automatic first run**: When neither an in-prompt inventory nor a saved inventory is available, start guided setup. A complete in-prompt inventory takes precedence and does not require a setup detour or a file write.
2. **Explicit re-configuration**: When the user asks for `choose-llm setup` or to edit their model list. Recognize the repository's older `how-to-choose-an-llm setup` wording when supplied in conversation; do not assume every agent registers it as a command.

### Setup protocol
Guide the engineer through four quick steps:

1. **List active models**: Ask which models the engineer actively uses across their providers (OpenAI, Anthropic, Google, Moonshot, DeepSeek, Groq, local Ollama/vLLM).
2. **Assign 1-10 ratings**:
   - **Intel (1-10)**: Task accuracy, reasoning depth, and instruction following.
   - **Speed (1-10)**: Responsiveness based on TTFT and TPOT.
   - **Cost (1-10)**: Economic efficiency (10 is free or cheapest, 1 is most expensive).
   - **Context**: Maximum usable token window.
   - **Open**: `true` if model weights are available, otherwise `false`. This says nothing about where a request runs.
   - **Hosting**: `local` for inference on the engineer's own machine or on-prem infrastructure; `private-cloud` for their controlled cloud deployment; `provider-api` for a third-party inference service; `unknown` when unconfirmed. Each row describes one actual deployment, not a possible future installation. Use separate row names when the same model is available through multiple deployments.
   - **Provider & tags**: Hosting vendor and task tags (for example: `"coding,reasoning,fast"`).
3. **Persist inventory**: Update the inventory the user chose. For a new inventory, default to `model-inventory.toon` in the current workspace so skill updates cannot overwrite it. Keep legacy skill-folder inventories readable; do not migrate or overwrite them without asking. This two-row schema example uses fictional names and ratings:
   ```toon
   models[2]{name,intel,speed,cost,context,open,hosting,provider,tags}:
     Example API,8.0,7.0,6.0,128000,false,provider-api,Example Provider,"general,fast"
     Example Local,9.0,5.0,4.0,256000,true,local,Own hardware,"reasoning,coding"
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
2. **Deployment boundary**: Determine permitted hosting (`local`, `private-cloud`, `provider-api`) separately from any open-weight requirement. For local-only or on-prem-only requests, require `hosting: local`. Clarify ambiguous "private" requirements, including whether controlled cloud infrastructure is acceptable.
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

### 2. Locate model inventory (first-run gate)

1. **In-prompt overrides**: Use the engineer's supplied models and ratings first. Ask only for fields necessary to resolve eligibility or scoring; never infer deployment from the model name or `open` flag. Do not persist a one-off override unless requested.
2. **Existing inventory**: Prefer an explicitly named inventory, then workspace `model-inventory.toon`, then the legacy skill-folder inventory. State which source is used; do not merge conflicting inventories silently.
3. **First run (no `model-inventory.toon` file made)**: If no `model-inventory.toon` file exists, the agent must immediately run the setup workflow. Do not output an empty state and do not tell the user to manually run a command. Directly launch the setup sequence:
   - Announce: `First run detected: no model-inventory.toon found. Running setup.`
   - Ask whether to adapt the illustrative starter (`model-inventory.example.toon`) or enter their own models. The starter is not a verified provider catalog.
   - Confirm access to each selected deployment, ratings, usable context, hosting, weight availability, and provider tags.
   - Save the confirmed table to `model-inventory.toon`.
   - After saving, immediately fulfill the engineer's original evaluation request using their new inventory.

### 3. Filter and calculate composite scores

Validate ratings as finite numbers from 1 to 10, context as a positive integer, `open` as a boolean, hosting against the defined enum, and deployment names as unique. Ask for corrections to malformed data rather than silently coercing it. Treat absent hosting in older inventories as `unknown`; it cannot satisfy a restricted hosting boundary until confirmed. A weights-available model is not necessarily installed, affordable on available hardware, or usable under the required license.

Use user-provided ratings as subjective inputs, not measured facts. For real recommendations, verify changing provider facts such as availability and usable context against current official documentation when tools allow. User access and actual deployment require user confirmation. If verification is unavailable, explicitly label the recommendation provisional. For synthetic tests, use the supplied fixture without researching fictional models. Open weights or self-hosting alone does not establish security or regulatory compliance.

#### Hard filter gates
Eliminate non-viable models before ranking:
- Discard models whose `context` is smaller than required context.
- Discard deployments outside the permitted hosting boundary. For local-only requests, reject `provider-api`, `private-cloud`, and `unknown`, even when `open: true`.
- If the user separately requires open weights, require `open: true`. Private hosting alone is not an open-weight requirement.
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

Choose `cost_saver` and `escalation` only from the same eligible set: highest cost-efficiency rating for the former, highest intelligence rating for the latter. Omit either when it offers no improvement over the selected model. Never bypass context, hosting, or quality gates for an alternative.

### 4. Output response in TOON format

Follow Agent eXperience Interface (AXI) standards: emit structured TOON output to save context tokens.

State the archetype, hard constraints, inventory source, and verification status with the result. Quote strings where needed. Multiline primitive arrays use `- ` list markers and exact counts; tables use the exact row count. Do not put Markdown backticks into structured values. When the user requests full details, include provider, hosting, tags, and context.

#### Standard recommendation format
Illustrative calculation using the starter ratings: reasoning weights, 128000 tokens, intelligence floor 6, provider APIs allowed. These names, capacities, and ratings are not verified current product specifications.
```toon
archetype: reasoning
inventory: illustrative starter
verification: unverified example
constraints:
  context: 128000
  min_intel: 6
  hosting: provider-api
selected:
  name: Fable 5.1
  score: 7.9
  intel: 9.0
  speed: 6.0
  cost: 5.0
  context: 256000
  open: false
  hosting: provider-api
cost_saver:
  name: Gemini 3.8 Flash
  cost: 9.0
  intel: 6.0
ranked[6]{rank,name,score,intel,speed,cost}:
  1,Fable 5.1,7.9,9.0,6.0,5.0
  2,GPT 6 Astra,7.65,8.5,7.0,5.0
  3,Opus 5,7.35,7.5,7.0,7.0
  4,Kimi K3,7.1,8.0,3.0,6.0
  5,GPT 5.6 Sol,7.0,7.0,7.0,7.0
  6,Gemini 3.8 Flash,6.8,6.0,8.0,9.0
help[2]:
  - "Ask for full details to include provider, hosting, tags, and context."
  - "Primary pick Fable 5.1 outscores runner-up by 0.25."
```

#### Definitive empty state
When zero models satisfy the constraints, state the zero explicitly with the blocking constraint:
```toon
models: 0 models found matching constraints (min_intel=9.5)
help[2]:
  - Review available inventory in model-inventory.toon
  - Ask for choose-llm setup to add higher intelligence models
```

---

## Baseline inventory reference

Read [model-inventory.example.toon](model-inventory.example.toon) only when the engineer chooses the starter. It is illustrative, unverified data, not a live catalog or confirmation of access. Confirm or replace the entries before saving a real inventory. Keep the baseline in that file rather than duplicating its table here.

---

## Technical reference

Read `references/five-questions-guide.md` for background on hosting economics, TTFT versus TPOT, benchmark contamination, and context window retrieval trade-offs.
