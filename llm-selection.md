# LLM Selection Decision

## Product: Perplexity.ai

## Decision Framework

| Decision Factor | My Choice | Reason (1 sentence) |
|-----------------|-----------|---------------------|
| Which LLM followed my prompt structure most faithfully? | Claude | Every required section appeared in the correct order with proper markdown headers, while Gemini occasionally reordered sections and DeepSeek sometimes merged fields. |
| Which LLM was most technically accurate (least hallucination)? | Claude | Consistently used hedging language ("likely uses," "plausible alternative," "unconfirmed") and explicitly separated confirmed facts from architectural inferences — the other two stated speculative technologies as facts. |
| Which LLM's output was most readable and well-organized? | Claude | Clean hierarchical structure with distinct "What's happening / Key technologies / Engineering challenge / Skill required / Honesty check" sections that made cross-layer comparison easy to scan. |
| Which LLM handled the "honesty check" best (admitting when a layer doesn't apply)? | Claude | Correctly rated Layer 2 as "Medium" relevance and Layer 6 as "Medium" while still providing valuable analysis. DeepSeek rated almost everything as "High" even where depth wasn't warranted. |

---

## Selected LLM for Final Tool

**Selected LLM:** Claude (Sonnet 4)

**Why:** Claude won 5 out of 6 layers on specificity, was the only model that self-calibrated uncertainty consistently (critical for a reusable tool that must be trustworthy), and followed the prompt template with the highest structural fidelity. While DeepSeek brought unique publicly-sourced data points (200B URLs, Cerebras partnership) and Gemini had strong systems-level insights (tail latency, KV-cache fragmentation), Claude's combination of depth, honesty, and format compliance makes it the best engine for a general-purpose teardown tool that must work reliably across any product — not just ML-heavy ones.

---

## Production Consideration

If this tool were running 100 times/day for a consulting firm:

- **Speed:** Claude was fastest at ~45s. For a tool that runs in batch (not user-facing real-time), even 90s (DeepSeek) would be acceptable, but Claude's speed advantage compounds at 100 runs/day — saving ~75 minutes daily vs DeepSeek.
- **Cost:** Claude API (Sonnet) is cost-competitive. DeepSeek is cheapest per token but thinking mode adds significant token overhead. Gemini Pro is free in AI Studio but rate-limited for production use.
- **Consistency:** Claude produced the most predictable output structure across both V1 and V2 runs. For a production tool where downstream parsing or client delivery depends on consistent formatting, this reliability matters more than occasional peak depth from thinking-mode models.
- **Final verdict for production:** Claude (Sonnet) — best balance of speed, structural consistency, and honesty calibration for a tool that must generalize across products and be trusted by clients without manual fact-checking of every output.
