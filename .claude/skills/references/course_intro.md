# Course Introduction — Full Stack AI Engineering

## What This Course Is

This is a comprehensive LLM developer certification course by **Towards AI**, teaching the core tech stack for building on top of Large Language Models:

- **Context Engineering** — Prompt Engineering, Data Engineering, Retrieval Augmented Generation (RAG)
- **Fine-Tuning** — Adapting LLMs to specific tasks and datasets
- **Tool Use & Agents** — Connecting LLMs into complex pipelines with external tool access

The course is delivered via 90 written lessons, code notebooks, videos, and instructor interaction. It targets software developers, ML engineers, data scientists, aspiring founders, and CS students.

## Learning Objectives

Students who complete this course are equipped to:
- Build and deploy production-ready RAG pipelines
- Fine-tune LLMs and embedding models
- Design and implement LLM agents with tool use
- Make informed decisions on model selection, data sourcing, and system architecture
- Submit a working LLM + RAG project for certification

## Approach and Philosophy

- **Intensely practical** — only the theory needed to use LLMs effectively, not training from scratch
- **Iterative and modular** — students build a Proof of Concept, then improve it incrementally toward an MVP
- **Model-agnostic** — demonstrates specific models (GPT-4o, Gemini 2.5, Claude, Llama) but teaches transferable principles
- **Full-stack** — covers data collection, pipeline design, evaluation, deployment, and production tips
- **Continuously updated** — new tools, methods, and case studies added monthly

## Core Tech Stack (used in course notebooks)

| Layer | Primary Tools |
|---|---|
| LLM APIs | OpenAI (GPT-4o, GPT-4o-mini), Google (Gemini 2.5 Flash/Pro), Anthropic (Claude), Meta (Llama via Together.ai), Ollama (local) |
| Frameworks | LlamaIndex (primary), LangChain (secondary), OpenAI Assistants |
| Embedding models | OpenAI, Cohere, HuggingFace |
| Vector databases | Covered within LlamaIndex and standalone |
| Data tools | Firecrawl, LlamaParse, Perplexity API, web search APIs |
| Evaluation | LLM-as-a-judge, RAG eval frameworks |
| Deployment | FastAPI, Gradio, HuggingFace Spaces |
| Observability | Langfuse, LangSmith, Phoenix |

**Default notebook model:** `gemini-2.5-flash` (unless the notebook specifies otherwise or the user requests a different model).

## Course Structure at a Glance

| Part | Focus |
|---|---|
| Part 0 | Course intro + LLM fundamentals (9 lessons) |
| Part 1 | Building the RAG AI Tutor end-to-end (60 lessons across 9 subsections) |
| Part 2 | Expanded tools, models, frameworks, and deployment optimizations (18 lessons) |
| Part 3 | Business strategy, niche selection, LLM ecosystem economics (9 lessons, optional) |
| Part 4 | Student's own LLM + RAG project for certification |

See `course_syllabus.md` for the full chapter-level breakdown with lesson titles.

## Prerequisites

- Basic Python coding ability (or ability to learn it in parallel)
- No deep AI/math background required
- Students without any coding background should first complete the *Python Primer for Generative AI* course

## The Capstone Project

To receive certification, students build and submit their own LLM + RAG application deployed on HuggingFace Spaces. It must use RAG and foundation model LLMs. A custom version of the AI Tutor is acceptable, but original projects are encouraged.
