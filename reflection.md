# Reflection — Day 5: AI Product Teardown Engine

## Product Analyzed: Perplexity.ai
## LLMs Compared: Claude (Sonnet 4), Gemini 2.5 Pro (Thinking), DeepSeek R1 (DeepThink)

---

### Question 1: "Which of the 6 layers surprised you the most in terms of complexity for the product you chose? Why?"

Layer 1 (Data Foundation) surprised me the most. Before this teardown, I thought of Perplexity as fundamentally an LLM product — a smart wrapper around GPT-4 with web search bolted on. The teardown revealed that the data layer is arguably more complex than the LLM layer itself. Perplexity operates a dual-path architecture where a pre-crawled index serves common queries at low latency, while a live fetch path makes real HTTP requests during active queries for recency-critical information. The engineering challenge I hadn't considered is citation integrity — when the system cites source [3], that page may have been updated or paywalled between crawl and the user clicking it. This is a compound data quality problem that cuts across storage, versioning, and real-time validation. It changed my understanding of where Perplexity's real defensibility lies: not in which LLM they call, but in the quality and freshness of the data they feed into it.

---

### Question 2: "What was the single biggest difference you noticed between the LLMs you tested? Not just 'one was better' — what specifically did one do that the others couldn't?"

The most significant difference was in confidence calibration. Claude consistently distinguished between confirmed facts and architectural inferences — using "likely uses," "plausible alternative," and "unconfirmed" throughout its analysis — while DeepSeek and Gemini presented speculative technology choices with the same assertive tone as verified facts. For a reusable teardown tool, this distinction is critical: if I show a client a teardown that states "Perplexity uses Qdrant for their vector store" as fact (when it's actually an informed guess), my credibility is at risk. Claude was the only model that built this epistemic hygiene into its output without being explicitly asked. On the other hand, DeepSeek brought something the others couldn't: concrete, publicly-sourced data points like the 200B URL count, the Cerebras partnership for 1200 tokens/sec Sonar inference, and the 23 Points of Presence — grounding its analysis in verifiable external evidence rather than pure architectural reasoning. This suggests the ideal workflow: use Claude for the core analysis and structure, then cross-reference with DeepSeek's outputs for any publicly-cited data points to add.
