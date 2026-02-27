# LLM Comparison — Product Teardown: Perplexity.ai

## Models Used
| # | LLM Name | Mode (Fast/Standard/Thinking) | Response Time (approx) |
|---|----------|-------------------------------|----------------------|
| 1 | Claude (Sonnet 4) | Standard | ~45s |
| 2 | Gemini 2.5 Pro | Thinking | ~60s |
| 3 | DeepSeek R1 | DeepThink (Thinking) | ~90s |

## Layer-by-Layer Comparison

### Layer 1: Data Foundation
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 4      | 4        | Claude |
| Named real tech?   | Y (Kafka, Qdrant, Weaviate, Elasticsearch, S3, dbt) | Y (Kafka, Qdrant, Milvus, Elasticsearch, Redis) | Y (S3/GCS, Flink, Cassandra/ScyllaDB, Airflow) | Claude |
| Identified a real engineering challenge? | Y — citation integrity under crawl latency | Y — parsing JS-heavy SPAs in real-time | Y — freshness vs completeness with ML-based prioritization | DeepSeek |
| Notes: | Claude uniquely identified the dual-path architecture (pre-crawled index vs live fetch) and the citation integrity problem. Most nuanced. | Gemini raised the SPA rendering challenge which others missed — a genuinely practical concern. | DeepSeek cited specific public scale numbers (200B URLs, 400PB storage), adding verifiable credibility. |

### Layer 2: Statistics & Analysis
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 4      | 3        | Claude |
| Named real tech?   | Y (Snowflake/BigQuery, Grafana, Prometheus, Scale AI, Thompson Sampling) | Y (Databricks/Snowflake, MLflow, Spark, LangSmith) | Y (SciPy, StatsModels, Prometheus, Vowpal Wabbit) | Claude |
| Identified a real engineering challenge? | Y — Goodhart's Law on quality metrics | Y — statistically quantifying hallucination at scale | Y — attribution of engagement changes to model updates | Claude |
| Notes: | Claude's Goodhart's Law analysis of proxy metric gaming was the most insightful take across all three. Gemini's hallucination quantification angle was also strong. DeepSeek was thinner here, staying more generic. |

### Layer 3: Machine Learning Models
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 4      | 4        | Claude |
| Named real tech?   | Y (E5, BGE, ColBERT, MiniLM, FAISS, HNSW, PyTorch, W&B) | Y (BGE, Nomic-Embed, Cross-Encoders, XGBoost, TensorRT, FAISS) | Y (Lucene, FAISS, ColBERT, MPNET, E5, MiniLM, ONNX, Triton) | Tie: Claude/DeepSeek |
| Named model family?| Y — three-stage cascade with latency per stage | Y — bi-encoder + cross-encoder + XGBoost pre-filter | Y — hybrid BM25+dense, two-tower, cross-encoder | Claude |
| Identified a real engineering challenge? | Y — real-time embedding index consistency during rolling crawl updates | Y — cross-encoder latency under GPU parallelization | Y — latency in multi-stage retrieval pipeline | Claude |
| Notes: | Claude's three-stage cascade (5ms query understanding → 50ms candidate retrieval → 100ms reranking) with specific latency budgets was the most architecturally detailed. Gemini uniquely added XGBoost pre-filtering as a practical heuristic step. |

### Layer 4: LLM / Generative AI
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 4      | 4        | Claude |
| Honest if not applicable? | N/A (High relevance — correctly identified) | N/A | N/A | — |
| Named RAG components? | Y — 5-step pipeline: query rewriting → parallel retrieval → chunking + reranking → context assembly → citation post-processing | Y — multi-model router, Sonar, speculative decoding, parametric memory suppression | Y — model-agnostic router, Sonar on Cerebras, heterogeneous fleet orchestration | Claude |
| Notes: | Claude broke the RAG pipeline into 5 distinct sequential sub-steps. Gemini uniquely highlighted the need to suppress LLM parametric memory — a non-obvious fine-tuning insight. DeepSeek added the Cerebras hardware partnership for Sonar inference speed (1200 tokens/sec). |

### Layer 5: Deployment & Infrastructure
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 4      | 4        | Claude |
| Named real tech?   | Y (vLLM, PagedAttention, EKS, Argo CD, CoreWeave, Langfuse, Datadog) | Y (Kubernetes, Triton, TensorRT-LLM, Prometheus, SageMaker HyperPod) | Y (Kubernetes, KServe, Seldon Core, ArgoCD, MLflow, Istio) | Claude |
| Notes: | Claude identified the specific KV cache memory pressure under bursty heterogeneous load with concrete examples (32K context Pro query vs 2K factual query). Gemini highlighted the cost-survival angle — inference cost vs subscription revenue. DeepSeek uniquely mentioned Istio service mesh for canary deployments. |

### Layer 6: System Design & Scale
| Criteria           | Claude | Gemini | DeepSeek | Best? |
|--------------------|--------|--------|----------|-------|
| Specificity (1-5)  | 5      | 5      | 4        | Tie: Claude/Gemini |
| Named real tech?   | Y (Cloudflare, Redis, DynamoDB, Kafka, PostgreSQL) | Y (gRPC, Cloudflare, Redis, SSE) | Y (Anycast, Cloudflare, Redis, Envoy, Firecracker) | DeepSeek |
| Notes: | Claude's semantic cache invalidation with query-type-dependent TTLs was the most unique insight. Gemini's tail latency mitigation during fan-out (wait vs drop slow sources) was genuinely non-obvious. DeepSeek uniquely mentioned Firecracker microVMs and 23 PoPs with Anycast routing. |

## Overall Verdict

| Dimension                          | Winner | Why? (1 sentence) |
|------------------------------------|--------|--------------------|
| Most technically specific overall  | Claude | Consistently provided per-stage latency budgets, named specific model architectures (E5, BGE, ColBERT), and identified unique tradeoffs at every layer. |
| Best at naming real technologies   | Claude | Named the most specific and granular tools (PagedAttention, Langfuse, dbt, Reciprocal Rank Fusion) while others stayed slightly higher-level. |
| Least hallucination / made-up info | Claude | Only model that consistently used hedging language ("likely uses," "plausible alternative," "unconfirmed") and explicitly separated confirmed facts from architectural inference. |
| Best at "hardest problem" insight  | Claude | Citation integrity across live-fetch vs index-snapshot paths is genuinely Perplexity-specific and couldn't apply to any other product's teardown. |
| Best structured output             | Claude | Followed the exact prompt template with all required sections in the specified order; Gemini occasionally reordered sections and DeepSeek sometimes merged fields. |
| Fastest useful response            | Claude | ~45s vs ~60s (Gemini) and ~90s (DeepSeek); thinking modes added latency without proportionally better depth. |

## Key Observation
> One thing I noticed about how different LLMs handle the same prompt:
>
> Claude was the only model that consistently self-calibrated its confidence level — using phrases like "likely uses," "plausible alternative," and "unconfirmed" — which is essential for a tool that shouldn't present guesses as facts. DeepSeek and Gemini stated technologies more assertively, which reads as confident but risks presenting speculation as truth. However, DeepSeek brought unique verifiable data points (200B URLs, Cerebras partnership, 23 PoPs, 1200 tokens/sec Sonar speed) that grounded its analysis in publicly sourced facts rather than pure inference — a different but equally valuable form of rigor. Gemini's standout skill was identifying practical engineering concerns that sound mundane but are genuinely hard (SPA parsing, tail latency in fan-out, KV-cache fragmentation), showing strong systems-thinking from an infrastructure perspective.
