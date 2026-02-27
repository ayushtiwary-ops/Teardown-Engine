# Best-Parts Map — Extracted from Multi-LLM Comparison

## Product: Perplexity.ai
## LLMs Compared: Claude (Sonnet 4), Gemini 2.5 Pro (Thinking), DeepSeek R1 (DeepThink)

---

| Layer | Best LLM for This Layer | What to Extract (key insight or paragraph) |
|-------|------------------------|-------------------------------------------|
| **Layer 1:** Data Foundation | **Claude** | Dual-path architecture insight: pre-crawled web index for low-latency retrieval + live fetch path (PerplexityBot) for recency-critical queries. Citation integrity under crawl latency as the core challenge — "when Perplexity cites source [3], that source may have been updated or paywalled between crawl and citation." Also the framing of Bing API as fallback/supplement rather than primary retrieval. |
| **Layer 2:** Statistics & Analysis | **Claude** | Goodhart's Law framing of quality metrics — "if you train your synthesis model to maximize citation click-through, it learns to produce answers that make users feel uncertain enough to click sources, not answers that are actually correct." Also the multi-armed bandit routing (Thompson Sampling) instead of classical A/B tests, and LLM-as-judge evaluation pipelines. |
| **Layer 3:** ML Models | **Claude** | Three-stage cascade with specific latency budgets: Stage 1 query understanding (~5ms, DistilBERT/GBT), Stage 2 candidate retrieval (<50ms, two-tower bi-encoder with E5/BGE + hybrid BM25 fusion via Reciprocal Rank Fusion), Stage 3 reranking (<100ms, cross-encoder). Training data scale estimate: tens of millions of query-document pairs. |
| **Layer 4:** LLM / Generative AI | **Claude** (with elements from **Gemini** and **DeepSeek**) | Claude's 5-step RAG pipeline breakdown was most complete. Extract Gemini's insight about suppressing parametric memory during fine-tuning — "forcing the LLM to aggressively suppress its pre-trained knowledge and rely exclusively on injected RAG context." Extract DeepSeek's Cerebras detail: Sonar achieving 1200 tokens/sec via specialized hardware partnership. |
| **Layer 5:** Deployment & Infra | **Claude** | KV cache memory pressure analysis: "A Pro query with 32K context tokens consumes 8-16x more GPU memory than a simple 2K factual query. Maximum load arrives simultaneously with maximum per-request memory demand when news breaks." The three failure modes (queuing → latency, model downgrade → quality drop, cold-start → 60-90s spin-up) were uniquely specific. |
| **Layer 6:** System Design & Scale | **Gemini** (with elements from **Claude**) | Gemini's tail latency insight during fan-out: "if 1 out of 5 websites takes 3 seconds to load, the system must intelligently decide whether to wait or drop it and generate with less context." Combine with Claude's semantic cache invalidation with query-type TTLs — "static factual queries cache for days, news queries may be stale in minutes." |
| **Overall Analysis / Hardest Problem** | **Claude** | Citation integrity across the live-fetch vs index-snapshot data path split: "by the time the LLM finishes generating a citation to source [3], source [3] may have been updated, paywalled, or taken down. Google doesn't cite live documents. OpenAI doesn't cite at sentence granularity. Bing cites but doesn't synthesize across sources. Perplexity uniquely commits to all three simultaneously." |
| **Writing Style / Structure** | **Claude** | Most faithful to the prompt template structure. Every section had all required fields in the correct order. Consistent use of markdown headers. Clean separation between "what's happening," technologies, challenges, and honesty checks. DeepSeek was second-best structurally. |

---

## How This Informed V2

Based on the extraction above, these are the specific improvements made to the V2 system prompt:

1. **Added relevance rating system (High/Medium/Low/Minimal)** — All three LLMs gave Layer 2 (Statistics) roughly equal depth as Layer 4 (LLM), even though Perplexity is fundamentally an LLM-native product. The adaptive depth instruction forces proper calibration.

2. **Added "what breaks if you remove this layer" requirement** — This constraint (inspired by Claude's V1 producing these naturally) forced the sharpest one-liners about dependencies and helped every layer analysis become more focused on what actually matters.

3. **Added few-shot example for Layer 3** — Claude's three-stage cascade analysis was so significantly better than the others that embedding a gold-standard Layer 3 example in the prompt helps anchor all LLMs to that level of specificity.

4. **Added negative example for hardest problem** — DeepSeek's V1 "hardest problem" was initially more generic ("freshness vs completeness" applies to any search engine). Adding the explicit instruction "'Scaling ML is hard' is NOT acceptable" pushes toward the kind of Perplexity-specific analysis Claude produced naturally.

5. **Added honesty/uncertainty calibration** — Claude naturally used "likely uses" and "plausible alternative" while DeepSeek and Gemini stated everything as fact. Added explicit rules requiring confidence flagging to bring all LLMs to Claude's honesty standard.

6. **Added summary table requirement** — None of the V1 outputs had a scannable summary table. This forces a useful artifact for quick comparison.
