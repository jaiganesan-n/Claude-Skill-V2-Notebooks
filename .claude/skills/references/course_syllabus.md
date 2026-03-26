# Course Syllabus — Full Stack AI Engineering

Use this file to identify which chapter a notebook belongs to, what concepts that chapter covers, what the prior chapter covered, and what comes next. This context shapes how a notebook should be written, what prerequisites it can assume, and what naming conventions to follow.

---

## Part 0 — Course Introduction and Understanding LLMs (9 Lessons)

**Goal:** Set context, cover LLM fundamentals, and build intuition for how and why LLMs behave the way they do.

**Lessons:**
- Video: Introduction
- Course Introduction and Logistics
- Syllabus In-Depth
- How To Use LLMs via API ← *first hands-on notebook*
- An LLM Developer's Role is to Address AI's Weaknesses and Increase its Capabilities
- Why Prompt Engineering, RAG, Tools, and Fine-Tuning?
- Understanding Large Language Models Without Math or Code
- Alien Intelligence Leans to Unintuitive Mistakes
- Course Tools Guide

**Prerequisites assumed by notebooks here:** None — entry level. Students may have no prior LLM experience.

---

## Part 1 — Core LLM Skills via Building Our RAG AI Tutor

**Goal:** Build a fully deployed RAG AI Tutor over ~60 lessons, learning each component as it's added to the project.

---

### Chapter 1 — Introduction to Using LLMs (11 Lessons)

**Covers:** LLM selection criteria, benchmarks, open vs. closed source, prompt engineering basics, prompt injection and hacking, prompt iteration.

**Lessons:**
- Video: Introduction to Part 1
- Video: LLM or Simpler Approach?
- Choosing your LLM Part 1: Open vs Closed Source and Key Comparison Metrics
- Choosing your LLM Part 2: Key Benchmarks and Leaderboards
- Choosing your LLM Part 3: The Leading Open and Closed LLMs Today
- Video: Prompting Basics
- Prompting 101 ← *notebook*
- Video: Prompt Hacking
- Building Our AI Tutor System Prompt and Introduction to Prompt Injection and Hacking ← *notebook*
- Evaluating and Iterating Prompts ← *notebook*

**Prerequisites assumed:** Basic Python. No RAG or LLM API experience expected yet.

---

### Chapter 2 — Basic RAG for Our Project (6 Lessons)

**Covers:** RAG concepts, vector databases, building a knowledge base, basic RAG pipeline from scratch, OpenAI GPTs.

**Lessons:**
- Video: Section Overview
- Video: What Is RAG?
- RAG 101
- Building Your Knowledge Base for RAG ← *notebook*
- Building a Basic RAG Pipeline From Scratch ← *notebook*
- Build an OpenAI GPT With Our AI Blog Data

**Prerequisites assumed:** LLM API usage (Chapter 1). Students know how to call an LLM and write basic prompts.

---

### Chapter 3 — Developing a RAG AI Tutor With LlamaIndex (7 Lessons)

**Covers:** LlamaIndex basics, vector databases in depth, improving data sources and prompts, RAG evaluation.

**Lessons:**
- Video: Section Overview
- Basic RAG With LlamaIndex ← *notebook*
- Video: How Vector Databases Work
- Using a Vector Database ← *notebook*
- Improving Data Sources and Prompts ← *notebook*
- Video: RAG Evals
- Evaluating Your RAG Pipeline ← *notebook*

**Prerequisites assumed:** Basic RAG (Chapter 2). Students have built a RAG pipeline from scratch.

---

### Chapter 4 — Using Other LLMs and Embedding Models (6 Lessons)

**Covers:** Security/privacy/cost considerations, open-source LLMs (Llama 3.1), Ollama, multimodal LLMs, embedding model selection.

**Lessons:**
- Video: Section Overview
- Security, Privacy & Cost Considerations; 5 Ways To Access LLMs
- Using Llama 3.1 70B on Together.ai and Comparing RAG Performance ← *notebook*
- Ollama Tutorial: Running Deepseek Distill Locally ← *notebook*
- Multimodal LLMs and Their Role in RAG Pipelines ← *notebook*
- How To Select the Right Embedding Model for Your Use Case? ← *notebook*

**Prerequisites assumed:** LlamaIndex RAG (Chapter 3). Students can build a basic RAG pipeline.

---

### Chapter 5 — Collecting, Filtering, and Cleaning Data (8 Lessons)

**Covers:** Web scraping, Firecrawl, Perplexity Web API, web search APIs, structured outputs, LlamaParse, building the AI Tutor dataset.

**Lessons:**
- Video: Section Overview
- Scraping Data From Websites ← *notebook*
- Scraping via API With Firecrawl ← *notebook*
- Perplexity Web API ← *notebook*
- Web Search API ← *notebook*
- Structuring Your Data: OpenAI Structured JSON Outputs ← *notebook*
- Parsing Data With LlamaParse ← *notebook*
- Choosing Data Sources and Building Our AI Tutor Dataset ← *notebook*

**Prerequisites assumed:** RAG pipeline with LlamaIndex (Chapter 3). Students know how to ingest and retrieve documents.

---

### Chapter 6 — Advanced RAG (10 Lessons)

**Covers:** Large context windows, chunk ranking, metadata filtering, hybrid search, query augmentation, re-ranking, advanced retrieval, indexing.

**Lessons:**
- Video: Section Overview
- More Input Context; Experimenting With More Chunks ← *notebook*
- LLM as a Judge; Ranking Chunks ← *notebook*
- Search With Metadata Filtering ← *notebook*
- Video: Advanced Search Methods
- Hybrid Search ← *notebook*
- Query Variation and Augmentation ← *notebook*
- Re-Ranking ← *notebook*
- Video: What Is Indexing
- Advanced Retrieval ← *notebook*

**Prerequisites assumed:** Basic RAG with LlamaIndex (Chapter 3) + custom dataset (Chapter 5).

---

### Chapter 7 — Fine-Tuning (6 Lessons)

**Covers:** Fine-tuning concepts, GPT-4o-mini fine-tuning, embedding model fine-tuning with LlamaIndex.

**Lessons:**
- Video: Section Overview
- Video: Fine-Tuning and RAG
- Fine-Tuning 101
- GPT4o-Mini Fine-Tuning In Our Project ← *notebook*
- Video: Fine-Tuning an Embedding Model
- Fine-Tuning Embedding Models With LlamaIndex ← *notebook*

**Prerequisites assumed:** Working RAG pipeline with custom dataset (Chapters 3–5).

---

### Chapter 8 — Expanded RAG Toolkit (10 Lessons)

**Covers:** Question validation, routing, agentic RAG, conversational memory, speech with GPT-4o, long context vs RAG, GraphRAG, reasoning models, reinforcement fine-tuning.

**Lessons:**
- Video: Section Overview
- Non-Technical Skills for Effective LLM Development
- Adding Question Validation and Routing to the System Prompt ← *notebook*
- An Introduction to Reasoning Language Models ← *notebook*
- Reinforcement Fine-Tuning for OpenAI ← *notebook*
- Enabling Conversational Memory in LLMs ← *notebook*
- Adding Speech With GPT-4o Audio ← *notebook*
- Video: Long Context vs RAG
- Long Context LLMs & Context Caching vs RAG ← *notebook*
- Graph ML and GraphRAG ← *notebook*

**Prerequisites assumed:** Full RAG pipeline (Chapters 3–6) + fine-tuning (Chapter 7).

---

### Chapter 9 — Building and Deploying the Final RAG Chatbot (5 Lessons)

**Covers:** Bringing together the full AI Tutor, FastAPI setup, Gradio UI, HuggingFace Spaces deployment, production tips.

**Lessons:**
- Video: Section Overview
- Bringing It All Together: Building the Final AI Tutor ← *notebook*
- How To Setup an API? ← *notebook*
- Building and Deploying a Gradio UI on Hugging Face Spaces ← *notebook*
- Quick Tips for Improving LLM Apps Once in Production

**Prerequisites assumed:** Everything from Chapters 1–8. This is the capstone of Part 1.

---

## Part 2 — More AI & LLM Models, Tools, Skills, and Frameworks (18 Lessons)

**Goal:** Broaden the toolkit with additional models, frameworks, and deployment techniques not central to the AI Tutor.

### Chapter 10 — More LLM Capabilities & Other Useful AI Models

**Covers:** Image generation (diffusion), speech models, OpenAI image API, LLM agents and tool use.

**Lessons:**
- Video: Section Overview
- Image Generation (Diffusion) and Speech Models
- Generating and Editing Images via the OpenAI API ← *notebook*
- LLM Agents: LLM Pipelines and Tool Use for Complex Tasks ← *notebook*

---

### Chapter 11 — More LLM Frameworks and Tools

**Covers:** LangChain vs LlamaIndex vs OpenAI Assistants, LangChain 101, HuggingFace APIs, OpenAI Assistants, MCP, observability tools (Langsmith, Phoenix, Langfuse), finding APIs.

**Lessons:**
- Video: Section Overview
- More LLM Framework Options: LangChain vs. LlamaIndex vs. OpenAI Assistants
- LangChain Introduction 101 ← *notebook*
- Access More AI Models With Hugging Face's APIs ← *notebook*
- Building Agents With OpenAI Assistants ← *notebook*
- LangChain OpenGPT
- Extending LLM Capabilities with Anthropic's Model Context Protocol (MCP)
- Observability or Tracing in Production; Langsmith, Phoenix, and Langfuse ← *notebook*
- How To Find More Useful APIs and Tools

---

### Chapter 12 — LLM Optimizations for Deployment

**Covers:** Pruning, distillation, quantization, speculative decoding, deploying a quantized LLM on GCP.

**Lessons:**
- Video: Section Overview
- Video: Pruning and Distillation
- Model Distillation and Teacher-Student Models
- LLM Deployment Optimisation; Quantization, Pruning, Speculative Decoding ← *notebook*
- Deploying a Quantized LLM on an Intel CPU on Google Cloud Platform (GCP) ← *notebook*

---

## Part 3 — LLM Ecosystem Economics (9 Lessons, Optional)

**Goal:** Strategic and business context for LLM product development.

**Covers:** Enterprise adoption drivers, who captures value, job category impact, 175 project ideas, product defensibility, deep dives on Perplexity, Consensus, Harvey AI.

**Prerequisites assumed:** Part 1 completion recommended but not required for reading these lessons. No notebooks in this part.

---

## Part 4 — Building Your Own LLM + RAG Project

**Goal:** Student's capstone — build and submit a working LLM + RAG application for certification.

**Requirements:**
- Must use RAG
- Must use foundation model LLMs (fine-tuned versions acceptable)
- Must be deployed on HuggingFace Spaces
- Custom AI Tutor variant is acceptable; original project is encouraged

---

## How to Use This Syllabus When Creating or Editing a Notebook

1. **Identify the chapter** from the notebook's filename, topic, or lesson title.
2. **Check the prerequisites** for that chapter — these set the floor for what students already know.
3. **Check the next chapter** — avoid teaching concepts the course covers in a later chapter.
4. **Use consistent naming** — notebook filenames should match or closely reflect the lesson title.
5. **Respect the project arc** — Part 1 notebooks are building toward the AI Tutor; every notebook should fit into that narrative.
