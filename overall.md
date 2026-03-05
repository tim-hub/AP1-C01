# AIP-C01 Exam — Complete Study Guide

Synthesized from 900 practice questions, official exam guide, and decision flowcharts.

---

## Exam Format & Scoring

- **65 scored + 10 unscored** questions (you can't tell which is which)
- Question types: Multiple choice, **multiple response** (select all correct), ordering, matching
- Pass score: **750/1000** (scaled)
- Compensatory scoring — you don't need to pass each domain individually
- No penalty for guessing

---

## The 5 Domains

| Domain | Weight | Focus |
|---|---|---|
| 1: Foundation Model Integration, Data Management & Compliance | **31%** | RAG, vector stores, embeddings, chunking, prompt engineering |
| 2: Implementation & Integration | **26%** | APIs, agents, deployment, enterprise patterns |
| 3: AI Safety, Security & Governance | **20%** | Guardrails, PII, IAM, compliance |
| 4: Operational Efficiency & Cost Optimization | **12%** | Pricing tiers, caching, batch inference |
| 5: Testing, Validation & Troubleshooting | **11%** | Evaluation methods, RAG quality, error handling |

> Domain 1 + 2 = **57% of the exam**. If you're short on time, nail those two first.

---

## Domain 1: Foundation Model Integration, Data Management & Compliance

### Vector Store Decision Tree

Pick based on these questions in order:

1. **100M+ docs with monthly updates?** → **S3 Vectors** (90% cost reduction)
2. **Need auto-scaling, no infra?** → **OpenSearch Serverless**
3. **Sub-millisecond latency?** → **ElastiCache for Redis**
4. **Need SQL/ACID transactions?** → **Aurora PostgreSQL + pgvector**
5. **Graph relationships (GraphRAG)?** → **Neptune Analytics**
6. **Full-text + vector hybrid search?** → **OpenSearch Service**

### Chunking Strategies

| Use Case | Strategy |
|---|---|
| General text | Default / Fixed-size |
| Code, structured data | Hierarchical (parent 1500 tokens, child 300) |
| Conversational | Semantic chunking |
| Large documents with sections | Hierarchical |

### Critical RAG Facts

- **Embedding model change = must re-index** — old vectors are incompatible, no exceptions
- **Titan Embeddings V2**: configurable dimensions — 256-dim gives 75% less storage with only 3% accuracy loss
- **Cohere Multilingual**: use when handling multiple languages
- **Reranking**: improves result relevance after initial retrieval (Bedrock reranker models)
- **Query expansion/decomposition**: use Lambda functions or Bedrock for complex queries

### Prompt Engineering

- **Bedrock Prompt Management**: parameterized templates, versioning, approval workflows
- **Bedrock Prompt Flows**: sequential chains, conditional branching (no-code)
- **Chain-of-thought**: improves reasoning for multi-step problems
- **DynamoDB**: store conversation history for multi-turn apps

---

## Domain 2: Implementation & Integration

### Which API to Use

| Scenario | Use |
|---|---|
| Simple single-turn call | `InvokeModel` |
| Multi-turn conversation | `Converse` API |
| Real-time streaming | `ConverseStream` or `InvokeModelWithResponseStream` |
| Model-agnostic (easy migration) | `Converse` API — change `modelId` only |
| Bulk processing | Batch Inference |

### Converse API — Key Gotcha

The Converse API has **no session management**. You must pass the **complete `messages` array** with every call — `role: user/assistant` + `content`. There is no `sessionId` parameter, no `X-Amzn-Bedrock-Context-Retention` header. The exam tests this constantly.

### Agents vs Knowledge Bases

| Need | Use |
|---|---|
| Autonomous multi-step tasks with tools | Bedrock Agents |
| Document Q&A / RAG retrieval | Knowledge Bases |
| Complex orchestration, human review | Step Functions |
| Async operations (return control) | Agent with return-control action |

> **Agents cannot integrate directly with Step Functions** — this is a frequent wrong answer.

### SageMaker Endpoint Selection

| Traffic Pattern | Endpoint Type |
|---|---|
| Consistent high traffic | Real-time endpoint |
| Variable/spiky traffic | Serverless Inference (no GPU) |
| Large payloads (>6MB) or long processing | Async Inference |
| Offline bulk | Batch Transform |
| Multiple models, similar traffic | Multi-model endpoint |
| Need scale-to-zero | Inference Components |
| GPU needed with variable traffic | SageMaker Real-time + scale-to-zero |

> **Lambda does NOT support GPU**. **SageMaker Serverless does NOT support GPU**. Any GPU scenario = SageMaker Real-time.

---

## Domain 3: AI Safety, Security & Governance

### PII Handling Decision

```
Need to RECOVER original PII later?
  → YES: ANONYMIZE mode + Tokenization service (Lambda stores PII mappings)
  → NO, but keep conversation context: ANONYMIZE mode (replaces with *email*)
  → NO, block everything: BLOCK mode (stops entire request)
```

### The 3 Anti-Patterns the Exam Loves to Test

1. **Never post-process with Comprehend** — PII is already logged in invocation logs before Comprehend sees it
2. **Never client-side redaction** — PII passes through the app layer, security risk
3. **Guardrails don't return confidence scores** — they only block or anonymize; no threshold-based scoring exposed to clients

### Monitoring Guardrails

- Metric: `InvocationsIntervened` in `AWS/Bedrock/Guardrails` namespace
- Dimension: `GuardrailPolicyType` — tells you which policy triggered (ContentFilter, DeniedTopic, SensitiveInformation)
- Enable **guardrail tracing** for detailed intervention logs

### IAM & Security

- Condition key: `bedrock:GuardrailIdentifier` — enforce specific guardrail usage via IAM
- **Model invocation logging**: disabled by default, must explicitly enable to capture prompts/responses
- **FIPS endpoints**: for data residency and compliance requirements
- **Cross-region inference**: no extra routing cost — priced at source region rates

---

## Domain 4: Operational Efficiency & Cost Optimization

### Cost Savings — Know Every Number

| Technique | Savings |
|---|---|
| Batch Inference | **50%** cost reduction |
| Prompt Caching | **90%** off cached tokens, **85%** latency reduction |
| Model Distillation | **75%** inference cost reduction |
| Intelligent Prompt Routing | **~30%** savings |
| Provisioned Throughput | **40–60%** off vs on-demand (with commitment) |
| S3 Vectors vs OpenSearch | **90%** storage cost reduction |
| Titan 256-dim embeddings | **75%** less storage vs 1024-dim |

### Service Tiers (Bedrock)

| Tier | Use When |
|---|---|
| Priority | Mission-critical, <100ms required |
| Standard | Normal production workloads |
| Flex | Non-urgent, latency-tolerant |

> If a question gives you high latency tolerance but selects Priority tier — that's a wrong answer. Use Flex.

### Prompt Caching — Key Limitation

- Cache TTL: **5 minutes** — only useful for repeated system prompts or common prefixes
- Don't apply caching to unique or user-specific content

---

## Domain 5: Testing, Validation & Troubleshooting

### Evaluation Methods

| Method | Use When |
|---|---|
| Programmatic metrics | Objective tasks (accuracy, F1, BLEU) |
| LLM-as-Judge | Subjective quality at scale (relevance, coherence) |
| Human evaluation | Highest quality bar, sensitive use cases |

### RAG Quality Metrics

- **Faithfulness**: Does the response stick to retrieved content?
- **Completeness**: Does the response cover all relevant retrieved info?
- **Context Relevance**: Did retrieval pull the right chunks?

### Error Handling

| Error | Correct Response |
|---|---|
| `ThrottlingException` | Exponential backoff **with jitter** (prevents thundering herd) |
| `ModelNotReadyException` | Retry up to 10 times with backoff |
| Inconsistent RAG results | Re-index after embedding model change |
| Large response truncation | Check guardrail streaming configuration |

> **Jitter is not optional** — "exponential backoff" without jitter is always a wrong answer on this exam.

---

## Master Anti-Pattern Table

| Scenario | Wrong Answer | Correct Answer |
|---|---|---|
| GPU inference | Lambda | SageMaker Real-time endpoint |
| PII protection | Post-process with Comprehend | Guardrails ANONYMIZE (pre-request) |
| Async agent pattern | Step Functions | Bedrock Agents with return-control |
| Embedding model changed | Keep existing vectors | Re-index all vectors |
| Variable traffic, high latency tolerance | Fixed Provisioned Throughput | Auto-scaling / Serverless / Flex tier |
| Recover original PII | BLOCK mode | ANONYMIZE + Tokenization |
| Multi-turn context | sessionId param | Full `messages` array each call |
| Throttling fix | Retry immediately | Exponential backoff WITH jitter |
| PII monitoring | Lambda post-hoc | CloudWatch `InvocationsIntervened` |

---

## Top Services by Frequency in Questions

| Service | Questions | % |
|---|---|---|
| Amazon Bedrock | 376 | 42% |
| Lambda | 162 | 18% |
| CloudWatch | 126 | 14% |
| EventBridge | 63 | 7% |
| S3 | 50 | 6% |
| IAM | 48 | 5% |
| SageMaker | 48 | 5% |
| DynamoDB | 43 | 5% |
| Kinesis | 39 | 4% |
| OpenSearch | 27 | 3% |

---

## Exam Strategy

1. **Domain 1+2 first** — 57% of the exam, highest ROI
2. **Memorize the anti-patterns** — the exam builds plausible-sounding wrong answers around them
3. **Numbers matter** — 50% batch, 90% prompt caching, 75% distillation, 5-min cache TTL
4. **"Most appropriate" questions** — always check if there's a cost or latency constraint buried in the scenario
5. **Multiple response questions** — read all options before selecting; they often pair one obviously right answer with one subtly wrong companion
