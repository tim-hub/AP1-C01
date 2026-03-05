# AIP-C01 — Official Study Notes Summary

Synthesized from personal study notes covering AI services, SageMaker, Bedrock, agent patterns, ML terms, and supporting AWS infrastructure.

---

## AI Services Beyond Bedrock

### Amazon Comprehend
NLP service — key capabilities:
- Key phrase extraction, sentiment analysis, entity recognition, language detection, syntax analysis, event detection
- **Detect PII** — but never use for post-processing after Bedrock invocation (PII already logged by then)
- **Comprehend Medical** → detects **PHI** (Protected Health Information), not generic PII

### Amazon Macie
- Discovers, monitors, and protects sensitive data (PII) **in S3** using ML and pattern matching
- Macie = S3-focused. Comprehend = text/NLP focused. They are not interchangeable.

### Other AI Services

| Service | Purpose |
|---|---|
| **Transcribe** | Audio/video to text, supports auto data masking |
| **Translate** | Language translation |
| **Textract** | Image/document to text (OCR) |
| **Lex** | Chatbot / conversational interfaces |
| **Polly** | Text-to-speech |
| **Kendra** | Semantic/NLP-based intelligent enterprise search |
| **QuickSight** | BI dashboards with ML-powered insights and natural language narratives |

### Kendra vs Bedrock Knowledge Bases for RAG

- **Kendra GenAI Index**: semantic search, NLP-based, traditional enterprise search patterns
- **Bedrock Knowledge Bases**: end-to-end managed RAG with embedding + vector store + FM integration
- Pick Bedrock KB when the question involves FM augmentation. Pick Kendra when it's enterprise search without a specific FM integration requirement.

---

## SageMaker — Details That Show Up in Questions

### Endpoint Comparison

| Type | GPU | Max Payload | Max Duration | On-demand |
|---|---|---|---|---|
| Real-time | Yes | 25 MB | Sub-ms latency | Yes |
| Serverless | **No** | 6 MB | Cold starts | Yes |
| Async | Yes | **1 GB** | **Up to 15 min** | Yes |
| Batch Transform | Yes | Large | Offline only | No |

> Async = large payloads + long processing + GPU support. Serverless = no GPU, small payloads, scale-to-zero.

### Training Data Input Modes

- **File mode**: copies entire dataset from S3 to instance before training — slow for large datasets
- **Fast File mode**: streams data from S3 on demand — better for large datasets
- **FSx for Lustre**: high-performance S3 reads, similar to Fast File mode but for very large datasets

### SageMaker Features

| Feature | Purpose |
|---|---|
| **Hyperparameter Tuning (warm start)** | Reuses previous tuning jobs as starting point |
| **Model Registry** | Centralized catalog for model versioning and deployment |
| **Model Cards** | Documents model characteristics and limitations |
| **Clarify** | Detects bias in data and model outputs |
| **Model Monitor** | Detects bias drift and data drift in deployed models; creates baseline from training data |
| **JumpStart** | Pretrained open-source models, no-code starting point |
| **Canvas** | No-code ML model building (absorbed Autopilot's UI) |
| **DeepAR** | Time-series forecasting using RNNs |
| **Data Wrangler / Processing** | Data preparation, transformation, and integration |
| `EnableNetworkIsolation=true` | Blocks internet + other AWS services during training |

---

## Amazon Bedrock — Key Details

- Fully managed, serverless — API access to third-party foundation models
- Can import models trained or fine-tuned in SageMaker AI
- **Cross-Region Inference**: improves model availability across regions; no extra routing cost
- **Bedrock Data Automation (BDA)**: automates processing of documents, images, video, and audio
- **Bedrock AgentCore**: platform for building, deploying, and operating agents at scale using any framework and FM

---

## Agent & Orchestration Patterns

### Strands Agents vs Agent Squad

- **Strands Agents**: SDK for building individual autonomous AI agents
- **Agent Squad**: multi-agent orchestration — use when multiple specialized agents need to coordinate

### Step Functions with Dynamic Choice States

Use Step Functions when you need:
- Content-based routing between different FM models
- Visual workflow monitoring and detailed tracing of routing decisions
- Built-in error handling and retry mechanisms

### ReAct Pattern

Agents alternate between **reasoning** about a task and **taking actions** to gather information or execute steps. Step Functions can implement ReAct patterns with chain-of-thought approaches.

### Circuit Breaker Pattern

- Temporarily blocks access to a faulty service after repeated failures
- Prevents cascading failures — used with Step Functions for FM resilience
- Different from retry: circuit breaker *stops* retrying temporarily to allow recovery

### CoT (Chain-of-Thought)

Improves multi-step reasoning by prompting the model to show its reasoning steps. Used via structured prompt design.

---

## General AI/ML Terms the Exam Tests

### Model Optimization Techniques

| Technique | What It Does |
|---|---|
| **Quantization** | Reduces numerical precision of weights — faster, more efficient inference |
| **Model Pruning** | Removes unnecessary weights/neurons to reduce model size |
| **Knowledge Distillation** | Transfers knowledge from large (teacher) to small (student) model |
| **LoRA** | Parameter-efficient fine-tuning — adds small trainable rank matrices to existing weights; faster and cheaper than full fine-tuning |

> Full fine-tuning adjusts all parameters — better performance but more resources. LoRA modifies only a small subset — faster, more efficient, may not always match full fine-tuning quality.

### Vector Search

- **kNN (exact)**: calculates exact distances to all points — accurate but slow; for small datasets
- **ANN (approximate)**: uses algorithms like **HNSW** (Hierarchical Navigable Small World) — fast with slight accuracy tradeoff; for large production datasets
- Always prefer ANN for production-scale vector databases

### Dataset Techniques

- **SMOTE**: Synthetic Minority Oversampling Technique — generates synthetic samples for the minority class in imbalanced datasets
- **L2 regularization (Ridge)**: prevents overfitting by penalizing large coefficient values
- **Data augmentation**: image rotation, flipping, scaling — increases effective training dataset size
- **Embedding drift**: when an app uses a different embedding model than the one that generated stored vectors — requires full re-indexing

### LLM-as-Judge

Uses one FM to evaluate outputs of another. Automated assessment of bias, toxicity, quality, and adherence to guidelines. Scales better than human evaluation for large-scale assessment.

### Other Terms

- **Recursive summarization**: technique for compressing long documents that exceed context window limits
- **CountVectorizer (scikit-learn)**: common text preprocessing for ML workflows
- **Deep Java Library (DJL)**: Java-based deep learning framework

---

## CloudWatch — What to Monitor

| What | How |
|---|---|
| Guardrail interventions | `InvocationsIntervened` metric with `GuardrailPolicyType` dimension |
| Token burst patterns | **CloudWatch Anomaly Detection** — ML-based automatic detection |
| Prompt/response analysis | **CloudWatch Logs Insights** |
| FM API call tracing | **X-Ray** — traces across service boundaries |
| Hallucination rates | Custom CloudWatch metrics + LLM-as-Judge evaluation |

> CloudWatch Anomaly Detection uses ML algorithms to analyze historical data and identify unusual patterns — correct answer for token burst detection questions.

---

## Infrastructure Services That Appear in Questions

### Edge & Hybrid

| Service | Use Case |
|---|---|
| **Local Zones** | AWS-managed infrastructure near large cities — low latency for gaming, media, finance |
| **Wavelength** | Inside 5G mobile operator networks — ultra-low latency for mobile/5G apps |
| **Outposts** | Physical AWS racks installed on-premises — same AWS APIs, for data sovereignty or on-prem requirements |
| **Snowball** | Active migration and physical transport of large data volumes |

> Local Zones = AWS manages the infra remotely. Outposts = physical rack in your building.

```
Gaming (low latency)          → Region + Local Zones
5G Modernisation              → Region + Outpost + Local Zones + Wavelength
Distributed 5G Edge Apps      → Region + Wavelength
Dedicated Industrial Edge     → Outpost
Large Data Migration          → Snowball
```

### Networking

| Service | Purpose |
|---|---|
| **PrivateLink (VPC Endpoints)** | One-way VPC-to-service connection — not bi-directional |
| **VPC Peering** | Bi-directional VPC-to-VPC connection |
| **Transit Gateway** | Hub for interconnecting multiple VPCs + on-premises (many-to-many) |
| **Direct Connect** | Dedicated physical connection from on-premises to AWS |

### Data Movement & Integration

| Service | Purpose |
|---|---|
| **EventBridge** | Complex event routing, multi-service integration, event bus |
| **SQS** | Reliable message delivery, point-to-point queuing, high volume |
| **Kinesis** | Data analytics pipelines — not for real-time UI interactions |
| **AppFlow** | Sync data between SaaS apps and AWS services |
| **Glue** | ETL, detects PII in pipelines |
| **Glue Data Catalog** | Centralized metadata repository for all data assets |
| **Lake Formation** | Fine-grained access control and governance for data lakes |

> EventBridge = complex event routing with multiple targets. SQS = simple reliable point-to-point queuing. They are not interchangeable.

### Security

| Service / Feature | Purpose |
|---|---|
| **SSE-S3** | S3-managed encryption — simpler, fewer logs |
| **SSE-KMS** | KMS-managed encryption — more audit logs, finer-grained key control |
| **Verified Permissions** | Fine-grained authorization service for your own applications |
| **Pinpoint** | Marketing communications — email, SMS, push notifications, voice |
| **AWS PrivateLink** | One-way private connectivity; VPC Peering is bi-directional |

---

## Quick "Which Service?" Traps

| Scenario | Wrong | Correct |
|---|---|---|
| Real-time UI streaming responses | Kinesis | WebSocket API + ConverseStream |
| Detect PII in S3 buckets | Comprehend | **Macie** |
| Detect PHI (health data) | Comprehend | **Comprehend Medical** |
| Multi-agent coordination | Bedrock Agents | **Agent Squad** |
| High-performance training data reads | File mode | **Fast File mode / FSx for Lustre** |
| Detect drift in deployed model | CloudWatch standard | **SageMaker Model Monitor** |
| Semantic enterprise search without RAG | Bedrock KB | **Kendra** |
| ANN vs kNN at scale | kNN (exact) | **ANN / HNSW** |

---

## Patterns Worth Memorizing

| Pattern | When to Apply |
|---|---|
| Exponential backoff **with jitter** | `ThrottlingException` — prevents thundering herd |
| Circuit Breaker | Cascading failure prevention with Step Functions |
| ReAct | Agent reasoning + action loops |
| CoT (Chain-of-Thought) | Multi-step reasoning improvement |
| Recursive summarization | Long documents exceeding context window |
| LLM-as-Judge | Automated quality evaluation at scale |
