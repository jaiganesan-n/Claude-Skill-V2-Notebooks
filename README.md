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
npx openskills install jaiganesan-n/Claude-Skill-V2-Notebooks
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
4. Dependency conflicts and outdated packages are checked (`pip check` + `pip list --outdated`)
5. If a `.env` file exists in the project root, its variables are auto-loaded before execution
6. The script is executed to verify correctness
7. On success, the script is converted back to `.ipynb` (create/edit only — test is read-only)
   - **notebook-create** saves to `notebooks/<name>.ipynb` (or `_v2`, `_v3`, … if the file already exists)
   - **notebook-edit** saves **all outputs** — replacements, updates, and versioned edits — to `notebooks-v/<name>_v2.ipynb` (or `_v3`, … if needed). The original in `notebooks/` is never overwritten.
   - A different output path can be requested explicitly
8. All temporary files are cleaned up

### Folder layout

```
notebooks/    ← originals (source of truth, never modified by notebook-edit)
notebooks-v/  ← all notebook-edit outputs (replacements, updates, versioned edits)
```

### Course awareness

The skills load three shared reference files before doing any work:

| File | Purpose |
|------|---------|
| `references/course_intro.md` | Default model, full tech stack, course structure, prerequisites |
| `references/course_syllabus.md` | Chapter-by-chapter lesson list — determines which content checks apply |
| `references/update_guidelines.md` | Pre-edit checklist (P1–P6), content checks (C1–C6), update report template |

**Per-notebook instructions always override course defaults.** If you specify a model, framework, library version, or scope in your prompt, that takes precedence over anything in the reference files. Conflicts are surfaced before work begins — nothing is silently overridden.

### notebook-edit behaviour

Every `notebook-edit` run follows these rules automatically:

| Rule | Behaviour |
|------|-----------|
| **Mandatory web search** | Before any edits, searches for latest documentation, deprecations, and breaking changes for every major library in the notebook |
| **Anti-loop** | Package conflict resolution is capped at 2 attempts per package — unresolved conflicts are documented and skipped to avoid getting stuck |
| **Latest implementation** | Deprecated API calls, changed import paths, and outdated SDK patterns found via web search are applied as required fixes |
| **Optional code section** | An `## Optional:` section is added at the end of every edited notebook with extended examples or alternative implementations |
| **Widgets** | `ipywidgets` are used in optional sections where they genuinely add interactivity (sliders, dropdowns, progress bars) |
| **Output folder** | All outputs go to `notebooks-v/` — originals in `notebooks/` are never touched |

### Update report

Every `notebook-edit` run produces a structured update report covering:
- G1–G7 general update guideline findings (web search results, package versions, output path, optional section)
- P1–P6 pre-edit check results (model names, API patterns, dependencies, key handling, runtime, grammar)
- Which content checks (C1–C6) were run and what was found
- Every change made and why
- Anything flagged but not changed, with the reason
- Per-notebook instructions received and how they affected scope

### notebook-test behaviour

`notebook-test` is read-only — it diagnoses and reports, never fixes. Each run:
- Checks for dependency conflicts with `pip check` before execution
- Lists outdated packages that could cause future issues
- Shows the **complete error list** to the user after execution (not truncated)
- Tags each error with its update check code (P1–P5) so `notebook-edit` knows exactly where to focus
- Waits for the author's comments before any fixes are applied

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

**Test, review errors, then edit based on findings:**

```
/notebook-test Run notebooks/rag_pipeline.ipynb and show me all errors
```
*(Review the error report, then:)*
```
/notebook-edit Fix the P2 API pattern errors and P3 dependency conflicts found in the test
```
