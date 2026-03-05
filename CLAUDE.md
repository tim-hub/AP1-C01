# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **study materials repository** for the AWS Certified Generative AI Developer - Professional (AIP-C01) exam. It contains no build system, tests, or runnable code — only structured content files (Markdown, JSON, PNG images).

## File Roles

| File/Directory | Purpose |
|---|---|
| `parsed_questions.json` | 900 structured exam questions — the primary data source. Upload to S3 for the RAG chatbot. |
| `MASTERY-GUIDE.md` | Distilled key decisions and patterns derived from all 900 questions |
| `BUILD-GUIDE.md` | Step-by-step guide to building a RAG chatbot on Amazon Bedrock |
| `fundamentals.md` | GenAI fundamentals primer |
| `decision-charts/DECISION-FLOWCHARTS.md` | Mermaid diagram source for all decision flowcharts |
| `decision-charts/*.png` | Exported flowchart images (generated from the Mermaid source) |
| `exam-guide/cd1.md–cd5.md` | Official AWS exam domain content |
| `exam-guide/Intro.md` | Exam overview (question types, scoring, domain weights) |
| `archive/` | Raw questions and processing scripts — gitignored |

## Exam Domain Weights

The official domain weights (from `exam-guide/Intro.md`) are:

- Domain 1: Foundation Model Integration, Data Management & Compliance — **31%**
- Domain 2: Implementation and Integration — **26%**
- Domain 3: AI Safety, Security, and Governance — **20%**
- Domain 4: Operational Efficiency and Optimization — **12%**
- Domain 5: Testing, Validation, and Troubleshooting — **11%**

`MASTERY-GUIDE.md` uses slightly different domain names/weights (derived from question analysis) — treat `exam-guide/Intro.md` as authoritative for official percentages.

## Content Conventions

- Flowcharts in `decision-charts/DECISION-FLOWCHARTS.md` use Mermaid syntax. When updating diagrams, also update or regenerate the corresponding `.png` exports.
- `parsed_questions.json` is the source of truth for question data. The `archive/` directory (gitignored) contains the raw processing scripts used to generate it.
- Key decisions and service selection patterns appear across multiple files (`MASTERY-GUIDE.md`, `README.md`, `decision-charts/`). Keep them consistent when updating any one of them.
