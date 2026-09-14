# Five questions reference guide

Technical reference for evaluating and selecting large language models across five operational dimensions.

## 1. Open or closed model

Evaluate where computation happens and who controls the weights.

### Closed models
- **Providers**: OpenAI, Anthropic, Google.
- **Delivery**: Managed endpoints with token billing.
- **Strengths**: Highest general reasoning ceiling, zero infrastructure maintenance, continuous provider-side optimizations.
- **Constraints**: Vendor lock-in, data shared with third-party providers, potential prompt or response logging, hard rate limits.

### Open-weight models
- **Hosting options**:
  1. **Self-hosted**: Run weights on owned hardware or private cloud instances (AWS EC2, Azure, GCP) using inference engines like vLLM, TGI, or SGLang.
  2. **API providers**: Groq, Together AI, Fireworks, DeepInfra host open models and bill per token or per compute second.
- **Strengths**: Complete data sovereignty, HIPAA or air-gapped compliance, deterministic model versioning with no stealth updates, customizable inference kernels.
- **Constraints**: GPU cluster provisioning, hardware idle costs, operational maintenance burden.

### Benchmark sources
- **Chatbot Arena (LMSYS)**: Blind pairwise human preference evaluations across closed and open models.
- **Open LLM Leaderboard (Hugging Face)**: Standardized open-weight benchmark tracking.

---

## 2. Cost dynamics

Cost breaks into variable token billing versus fixed infrastructure overhead.

### Pay-per-token economics
- Providers bill separately for input (prompt) and output (completion) tokens.
- Output tokens usually cost two to four times more than input tokens due to autoregressive generation mechanics.
- Prompt caching discounts repeated system prompts and reference context up to 80 to 90 percent.

### Fixed hosting economics
- Self-hosting trades variable token costs for fixed GPU instance rates.
- An 8x H100 instance runs at a flat hourly cost whether it handles 10 requests or 10,000 requests.
- Self-hosting becomes cheaper only when request volume consistently saturates GPU capacity. For sparse or spiky traffic, third-party APIs cost significantly less.

### Cost rating convention
In this system, cost uses an inverted scale:
- **10**: Negligible cost (local small model, free tier, or micro-fractions of a cent per request).
- **5**: Moderate production expense (standard tier models like GPT-4o mini, Flash, or mid-tier open models).
- **1**: Extreme cost (frontier reasoning models processing massive input and reasoning tokens).

---

## 3. Latency dynamics

Latency dictates whether a model can power realtime interfaces or must stay in background queues.

### The two primary metrics
1. **Time to first token (TTFT)**:
   - Measures prompt ingestion and initial activation.
   - Governed by prompt length, KV cache hits, and server queuing.
   - Vital for interactive UIs where users expect immediate visual feedback.
2. **Time per output token (TPOT)**:
   - Measures generation speed for each subsequent token.
   - Governed by model parameter count, memory bandwidth, and quantization level.
   - Dictates reading comfort: human reading speed averages 4 to 8 tokens per second. TPOT should exceed 30 tokens per second for comfortable streaming.

### Inference optimizations
- **Quantization**: FP8, INT8, and INT4 reduce memory bandwidth pressure and increase throughput with minimal quality loss.
- **Speculative decoding**: A tiny draft model proposes tokens verified in parallel by the target model.
- **Hardware engines**: Dedicated inference accelerators (such as LPUs) achieve extreme token output speeds.

---

## 4. Performance and intelligence

Parameter count alone does not guarantee task success.

### Intelligence tiers
- **Tier 1 (Intel 5.0 to 6.5)**: Deterministic classification, structured schema formatting, simple extraction, conversational routing.
- **Tier 2 (Intel 7.0 to 8.0)**: Multi-turn agent tool execution, full-function coding, balanced writing, contextual analysis.
- **Tier 3 (Intel 8.5 to 10.0)**: Novel architecture design, deep multi-step logic, complex math, edge-case vulnerability detection.

### Reasoning models
- Models that produce thinking tokens before final answers (like OpenAI o1 or DeepSeek R1).
- Higher accuracy on competitive programming, formal logic, and hard mathematics.
- Trade-off: high latency (thinking takes 5 to 60 seconds) and high token cost (reasoning tokens are billed as output).

### Task-specific evals versus public benchmarks
- Public benchmarks degrade over time through dataset contamination and targeted model training.
- Use application-level evals: build a test harness with 50 to 200 real examples from your product. Grade outputs with deterministic assertions or calibrated LLM judges.

---

## 5. Context window architecture

Context capacity defines how much text, code, or history the model can process in one pass.

### Scale tiers
- **Small (8k to 16k tokens)**: Single-turn tasks, small snippets, chat with active memory pruning.
- **Standard (32k to 128k tokens)**: Full multi-turn chats, small documentation sets, multi-file code editing.
- **Massive (200k to 2M+ tokens)**: Large codebases, full books, multi-hour meeting transcripts, large video or audio dumps.

### Practical limits of massive windows
- **Cost amplification**: Sending 500k tokens on every turn multiplies API bills rapidly unless prompt caching is active.
- **Needle-in-a-haystack degradation**: Retrieval precision drops when relevant facts sit buried in the middle of long contexts.
- **Latency impact**: Prompt processing time grows with context size, spiking TTFT.
