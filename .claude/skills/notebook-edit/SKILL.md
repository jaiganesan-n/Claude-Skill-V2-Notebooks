---
name: notebook-edit
description: Edit an existing Jupyter notebook -- make changes, test execution, and convert back.
---

# notebook-edit

**Description:** Edit an existing Jupyter notebook -- make changes, test execution, and convert back.

---

## Shared Workflow

**Read [../notebook-create/NOTEBOOK_WORKFLOW.md](../notebook-create/NOTEBOOK_WORKFLOW.md) first.** It covers the two-environment architecture, jupytext setup, dependency validation, API key management, LLM defaults, error handling, and cleanup.

---

## Key Principle

**Make minimal, focused changes** unless the user explicitly requests broader modifications. Change only what is needed to fulfill the user's request. Don't refactor surrounding code, add features, or restructure the notebook beyond what was asked for.

**Never alter the core flow, pipeline, or idea of the notebook** during updates, edits, or testing — unless the user explicitly instructs you to do so. This means:
- The sequence of steps the notebook teaches must remain intact
- The central concept or technique being demonstrated must not change
- The overall pipeline architecture (e.g., ingest → chunk → embed → retrieve → generate) must be preserved
- If an update would require changing the core flow to work correctly, **stop and ask the user** rather than proceeding silently

---

## Workflow

### 0. Load Update Context

Before making any changes, read:
- [`../references/update_guidelines.md`](../references/update_guidelines.md) — pre-edit checklist, content checks, and the update report template
- [`../references/course_syllabus.md`](../references/course_syllabus.md) — identifies the chapter, which determines which Content & Concept Checks apply
- [`../references/course_intro.md`](../references/course_intro.md) — confirms default model, expected tech stack, and course philosophy

**Per-notebook instructions take precedence over all defaults.**
If the user has provided targeted instructions for this specific edit (e.g. "only update the API pattern, don't touch anything else", "switch this notebook to use LangChain instead of LlamaIndex", "ignore P3 — dependency versions are pinned intentionally"), follow those instructions. They override the general defaults in course_intro.md and the default scope of update_guidelines.md.

Do not silently skip a check that the user has explicitly told you to skip — instead, note it in the "Items Skipped" section of the update report with the reason: "user instruction".

Work through the pre-edit checklist from `update_guidelines.md` for all checks that are NOT excluded by per-notebook instructions. Document all findings. Include the complete update report in your final response using the Update Report Template from `update_guidelines.md`.

**Web search for latest implementation patterns.** Before making edits, search for any recent changes to the major libraries used in the notebook (e.g. `anthropic`, `langchain`, `llama-index`, `openai`, `gradio`, `transformers`). Look for:
- Deprecated methods or changed import paths since the notebook was last updated
- New recommended patterns for SDK initialization, API calls, or tool use
- Current stable version numbers for the packages being installed

Document what you find. If the search reveals a breaking change or a clearly better pattern, apply it as part of the edit. If it would change the core flow, flag it to the user first.

**Notebook readability check.** Before making code changes, read the first few cells of the notebook. Verify the notebook opens with a clear purpose header — it should include:
- A title cell stating what the notebook is about
- What the notebook covers (the concepts or techniques demonstrated)
- Learning objectives (what the learner will be able to do after completing it)
- Prerequisites (what prior knowledge is assumed)
- A "What's New" or targeted update notes section if this is an updated version

If any of these are missing or vague, add or improve them as part of the edit — this counts as a P6-level fix and should be noted in the update report under Changes Made.

**Read `update_log.json` before editing.** Check if `$PROJECT_DIR/update_log.json` exists. If it does, read it and review which targeted additions have already been logged for other notebooks. Use this to avoid introducing the same specific addition (e.g. a widget, an optional pipeline cell, a new section) into multiple notebooks redundantly. The log only tracks targeted/specific additions — general updates (dep versions, model names, API patterns, typo fixes) are never logged there.

### 1. Setup

```bash
PROJECT_DIR="$(pwd)"
TEMP_DIR="/tmp/notebook_test_$(date +%s)"
mkdir -p "$TEMP_DIR"
cd "$TEMP_DIR"
```

Ensure `.venv_tools` exists (see shared workflow for jupytext setup).

### 2. Convert Notebook to Python

```bash
$PROJECT_DIR/.venv_tools/bin/jupytext --to py $PROJECT_DIR/notebooks/<notebook_name>.ipynb --output <notebook_name>.py
```

### 3. Install Existing Dependencies

Extract and install the dependencies from the notebook:

```bash
grep -E "^# !pip install" <notebook_name>.py | sed 's/^# !pip install //' > requirements.txt
uv venv venv_notebook
uv pip install --python venv_notebook $(cat requirements.txt) 2>&1 | tee install_log.txt
```

If installation fails due to dependency conflicts, follow the dependency validation workflow in the shared workflow to resolve them. When adding new dependencies, check `$PROJECT_DIR/.claude/skills/notebook-create/colab_libraries.txt` to ensure version compatibility with Colab.

### 3.5. Check and Update Package Versions

After a successful install, check whether any pinned packages have newer stable versions available:

```bash
venv_notebook/bin/python -m pip list --outdated --format=columns 2>/dev/null | tee outdated_packages.txt
cat outdated_packages.txt
```

For each outdated package:
1. Check `$PROJECT_DIR/.claude/skills/notebook-create/colab_libraries.txt` — if Colab pins an older version of that package or a dependency, do **not** upgrade beyond what Colab supports.
2. If upgrading is safe (no Colab conflict), bump the version in `requirements.txt` and re-run the install to verify no new conflicts appear.
3. After resolving, re-run the full dependency validation workflow from the shared workflow to confirm pinned versions are consistent.
4. Update the `!pip install` line in the `.py` file with the final validated versions.

Document every version change (or decision not to change) in the update report under P3.

### 4. Make Edits

Edit the `.py` file to implement the user's requested changes. Keep changes minimal and focused.

**Always fix grammar and typos** in user-facing strings within code cells (e.g. print messages, prompts, LLM instructions, comments shown to users). Do this alongside the requested changes — no need for the user to ask separately.

If you add or change dependencies:
- Re-validate using the full dependency validation workflow from the shared workflow
- Update the `!pip install` line in the `.py` file with the verified versions

### 5. Test Execution

**Verify `.env` before running.** Read `$PROJECT_DIR/.env` (if it exists) and cross-check it against the API keys the notebook needs. Identify what the notebook imports (e.g. `GOOGLE_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `TOGETHER_API_KEY`) and confirm each required key is present in `.env`. If a key is missing:
- Do not hardcode it — stop and inform the user which key is missing and how to add it to `.env`.
- If `.env` does not exist at all, warn the user and note that tests will fail for any notebook that requires API credentials.

```bash
# Show which keys are defined in .env (values masked)
if [ -f "$PROJECT_DIR/.env" ]; then
  grep -v '^#' "$PROJECT_DIR/.env" | grep '=' | sed 's/=.*/=<set>/' | sort
else
  echo "No .env file found at $PROJECT_DIR/.env"
fi
```

Before running, read the `.py` file and use the Edit tool to remove any Colab-specific lines (see the shared workflow's "Colab-Specific Code" section for patterns). Remember what you removed and where — you will need to restore them in step 6.

```bash
# Load .env if it exists
if [ -f "$PROJECT_DIR/.env" ]; then
  export $(grep -v '^#' "$PROJECT_DIR/.env" | xargs)
fi
uv run --python venv_notebook/bin/python <notebook_name>.py
```

If errors occur, fix them in the `.py` file and re-test. See the shared workflow for error handling guidance.

### 6. Convert Back to Notebook

Before converting, restore any Colab-specific lines that were removed in step 5. Use the Edit tool to add them back in their original positions in the `.py` file.

Once restored, determine the versioned output path (never overwrite the original) and convert:

```bash
# Auto-increment version suffix — start at _v2
ORIG_DIR="$(dirname "$PROJECT_DIR/notebooks/<notebook_name>.ipynb")"
BASE="$ORIG_DIR/<notebook_name>"
VERSION=2
OUTPUT="${BASE}_v${VERSION}.ipynb"
while [ -f "$OUTPUT" ]; do
  VERSION=$((VERSION + 1))
  OUTPUT="${BASE}_v${VERSION}.ipynb"
done
$PROJECT_DIR/.venv_tools/bin/jupytext --to notebook <notebook_name>.py --output "$OUTPUT"
echo "Saved as: $OUTPUT"
```

If the user specifies a different output path or filename, use that instead.

### 7. Cleanup

```bash
cd "$PROJECT_DIR"
rm -rf "$TEMP_DIR"
```

### 8. Update `update_log.json` — MUST DO

After a successful edit, update `$PROJECT_DIR/update_log.json` with any **targeted additions** made in this session.

**What to log** — specific, non-redundant additions only:
- New pipeline cells or pipeline variations
- Optional code blocks or alternate implementations
- New widgets, interactive controls, or visualizations
- New content sections or unique demonstrations added to this notebook

**What NOT to log** — general updates are excluded:
- Dependency version bumps
- Model name or API pattern updates
- Grammar, typo, or readability fixes
- Structural or formatting changes

**Entry format** — 4–5 words maximum per entry, no full sentences.

**JSON structure:**
```json
{
  "<notebook_name>": {
    "v2": ["optional reranker cell", "progress widget added"],
    "v3": ["async retrieval option"]
  }
}
```

Each key under a notebook is the version suffix of the output file (`v2`, `v3`, etc.). Each version only lists what is **new in that version** — do not copy prior-version entries forward.

**Procedure:**

1. Check if `$PROJECT_DIR/update_log.json` exists.
   - If it exists: read it, find the entry for the current notebook, add the new version key with its entries.
   - If it does not exist: create it with only the current notebook's entry.
2. If the user has **deleted a notebook**, remove its key from the log entirely.
3. Write the updated log back using the Edit or Write tool. Never overwrite unrelated notebook entries.

### 9. Write Update Report

Using the Update Report Template from `update_guidelines.md`, complete the report and deliver it to the user alongside the output notebook path. Include:
- All P1–P6 findings (checked — no issues is a valid entry)
- Which Content & Concept Checks (C1–C6) were run and what was found
- Every change made and why
- Anything flagged but not changed, and the reason
- Per-notebook instructions received and how they affected the scope (list any default checks skipped or defaults overridden, with reason)

---

## Quick Reference

```bash
# 1. Setup
PROJECT_DIR="$(pwd)"
TEMP_DIR="/tmp/notebook_test_$(date +%s)"
mkdir -p "$TEMP_DIR" && cd "$TEMP_DIR"

# 2. Convert to Python
$PROJECT_DIR/.venv_tools/bin/jupytext --to py $PROJECT_DIR/notebooks/<notebook_name>.ipynb --output <notebook_name>.py

# 3. Install existing dependencies
grep -E "^# !pip install" <notebook_name>.py | sed 's/^# !pip install //' > requirements.txt
uv venv venv_notebook
uv pip install --python venv_notebook $(cat requirements.txt)

# 3.5. Check for outdated packages (cross-check against colab_libraries.txt before upgrading)
venv_notebook/bin/python -m pip list --outdated --format=columns 2>/dev/null | tee outdated_packages.txt

# 4. Make edits to <notebook_name>.py
#    - Minimal changes + fix grammar/typos in strings
#    - Add/improve notebook purpose header if missing (title, what it covers, objectives, prerequisites)
#    - Apply any version bumps resolved in step 3.5
#    - Apply any API/SDK pattern updates found via web search

# 5. If deps changed: re-validate (see shared workflow)

# 6. Verify .env keys before testing
if [ -f "$PROJECT_DIR/.env" ]; then
  grep -v '^#' "$PROJECT_DIR/.env" | grep '=' | sed 's/=.*/=<set>/' | sort
else
  echo "No .env file found — API key tests will fail"
fi

# 7. Remove Colab-specific lines from <notebook_name>.py (Edit tool)
#    (IPython kernel shutdown, drive.mount, files.upload, etc. — see shared workflow)
#    Remember removed lines and their positions for restoration in step 9.

# 8. Test
if [ -f "$PROJECT_DIR/.env" ]; then export $(grep -v '^#' "$PROJECT_DIR/.env" | xargs); fi
uv run --python venv_notebook/bin/python <notebook_name>.py

# 9. Fix errors and re-test until passing

# 10. Restore removed Colab-specific lines in <notebook_name>.py (Edit tool)

# 11. Determine versioned output path and convert back to notebook
ORIG_DIR="$(dirname "$PROJECT_DIR/notebooks/<notebook_name>.ipynb")"
BASE="$ORIG_DIR/<notebook_name>"; VERSION=2; OUTPUT="${BASE}_v${VERSION}.ipynb"
while [ -f "$OUTPUT" ]; do VERSION=$((VERSION+1)); OUTPUT="${BASE}_v${VERSION}.ipynb"; done
$PROJECT_DIR/.venv_tools/bin/jupytext --to notebook <notebook_name>.py --output "$OUTPUT"
echo "Saved as: $OUTPUT"

# 12. Cleanup
cd "$PROJECT_DIR" && rm -rf "$TEMP_DIR"

# 13. Update update_log.json (MUST DO — targeted additions only, 4-5 words each)
#     Check if $PROJECT_DIR/update_log.json exists, then read → update → write
#     Log: new pipelines, optional cells, widgets, unique content sections
#     Skip: dep bumps, model names, API pattern fixes, typos
#     Structure: { "notebook_name": { "v2": ["entry one", "entry two"], "v3": [...] } }
#     Remove notebook key if the notebook has been deleted
```
