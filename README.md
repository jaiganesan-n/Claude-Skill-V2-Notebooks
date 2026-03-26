# Notebook Skills

Claude Code skills for creating, editing, and testing Jupyter notebooks — built for the **Full Stack AI Engineering** course by Towards AI.

The skills are course-aware: they read shared reference files (`course_intro.md`, `course_syllabus.md`, `update_guidelines.md`) to apply the correct tech stack, default model, and pre-edit checks for each chapter automatically.

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **notebook-create** | `/notebook-create` | Create a new notebook from scratch with validated dependencies |
| **notebook-edit** | `/notebook-edit` | Edit an existing notebook with minimal, focused changes and a full update report |
| **notebook-test** | `/notebook-test` | Test a notebook's execution without modifying it |

All three skills share a common workflow defined in `.claude/skills/notebook-create/NOTEBOOK_WORKFLOW.md`.

## Installation

These skills are installed at the **project/workspace level** (not user level) using [openskills](https://github.com/numman-ali/openskills).

From the root of your target project:

```bash
npx openskills install towardsai/notebook-skills
```

This installs the skills into `.claude/skills/` in your project directory. They are immediately available in Claude Code — no restart needed.

### Manual installation

If you prefer not to use openskills, clone the repo and copy the skills directory:

```bash
git clone https://github.com/your-org/your-skills.git /tmp/notebook-skills
cp -r /tmp/notebook-skills/.claude/skills/ .claude/skills/
rm -rf /tmp/notebook-skills
```

## Prerequisites

- [uv](https://docs.astral.sh/uv/) — fast Python package installer
- [Claude Code](https://claude.ai/code) CLI

The skills will set up jupytext and other tools automatically on first use.

## How It Works

1. A temporary directory is created in `/tmp` for isolated execution
2. Notebooks are converted to `.py` scripts using [jupytext](https://github.com/mwouts/jupytext)
3. Dependencies (from `!pip install` cells) are extracted and installed in a temporary venv
4. If a `.env` file exists in the project root, its variables are auto-loaded before execution
5. The script is executed to verify correctness
6. On success, the script is converted back to `.ipynb` (create/edit only — test is read-only)
   - **notebook-create** saves to `notebooks/<name>.ipynb` (or `_v2`, `_v3`, … if the file already exists)
   - **notebook-edit** saves next to the original as `<name>_v2.ipynb` (or `_v3`, … if needed) — the original is never overwritten
   - A different output path can be requested explicitly
7. All temporary files are cleaned up

### Course awareness

The skills load three shared reference files before doing any work:

| File | Purpose |
|------|---------|
| `references/course_intro.md` | Default model, full tech stack, course structure, prerequisites |
| `references/course_syllabus.md` | Chapter-by-chapter lesson list — determines which content checks apply |
| `references/update_guidelines.md` | Pre-edit checklist (P1–P6), content checks (C1–C6), update report template |

**Per-notebook instructions always override course defaults.** If you specify a model, framework, library version, or scope in your prompt, that takes precedence over anything in the reference files. Conflicts are surfaced before work begins — nothing is silently overridden.

### Update report

Every `notebook-edit` run produces a structured update report covering:
- P1–P6 pre-edit check results (model names, API patterns, dependencies, key handling, runtime, grammar)
- Which content checks (C1–C6) were run and what was found
- Every change made and why
- Anything flagged but not changed, with the reason
- Per-notebook instructions received and how they affected scope

### Environment variables

Place a `.env` file in the project root with your API keys.

For Example:
```
GOOGLE_API_KEY=your-key-here
OPENAI_API_KEY=your-key-here
ANTHROPIC_API_KEY=your-key-here
TOGETHER_API_KEY=your-key-here
```

The skills automatically load it before running notebooks. Only set the keys your notebooks actually need.

## Example Prompts

**Create a notebook:**

```
/notebook-create Create a notebook that uses LangChain with Gemini to summarize a webpage
```

**Override course defaults:**

```
/notebook-create Create a notebook about vector embeddings.
Use OpenAI text-embedding-3-small instead of the course default embedding model.
```

**Provide style references:**

```
/notebook-create Create a notebook about RAG with ChromaDB.
Use notebooks/langchain_basics.ipynb as a style reference.
```

**Point to documentation or web resources:**

```
/notebook-create Create a notebook showing LangGraph agents.
Look up the latest LangGraph docs to use the current API — the library has changed recently.
```

**Edit with targeted scope:**

```
/notebook-edit Update notebooks/rag_pipeline.ipynb.
Only update the API pattern — don't touch anything else.
```

```
/notebook-edit Update notebooks/rag_pipeline.ipynb.
Check https://docs.trychroma.com/migration for breaking changes in ChromaDB v0.5 and update the code accordingly.
```

**Test a notebook:**

```
/notebook-test Run notebooks/langchain_basics.ipynb and report any errors
```
