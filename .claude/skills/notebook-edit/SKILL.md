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

### 4. Make Edits

Edit the `.py` file to implement the user's requested changes. Keep changes minimal and focused.

**Always fix grammar and typos** in user-facing strings within code cells (e.g. print messages, prompts, LLM instructions, comments shown to users). Do this alongside the requested changes — no need for the user to ask separately.

If you add or change dependencies:
- Re-validate using the full dependency validation workflow from the shared workflow
- Update the `!pip install` line in the `.py` file with the verified versions

### 5. Test Execution

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

### 8. Write Update Report

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

# 3. Install dependencies
grep -E "^# !pip install" <notebook_name>.py | sed 's/^# !pip install //' > requirements.txt
uv venv venv_notebook
uv pip install --python venv_notebook $(cat requirements.txt)

# 4. Make edits to <notebook_name>.py (minimal changes + fix grammar/typos in strings)

# 5. If deps changed: re-validate (see shared workflow)

# 6. Remove Colab-specific lines from <notebook_name>.py (Edit tool)
#    (IPython kernel shutdown, drive.mount, files.upload, etc. — see shared workflow)
#    Remember removed lines and their positions for restoration in step 8.

# 7. Test
if [ -f "$PROJECT_DIR/.env" ]; then export $(grep -v '^#' "$PROJECT_DIR/.env" | xargs); fi
uv run --python venv_notebook/bin/python <notebook_name>.py

# 8. Fix errors and re-test until passing

# 9. Restore removed Colab-specific lines in <notebook_name>.py (Edit tool)

# 10. Determine versioned output path and convert back to notebook
ORIG_DIR="$(dirname "$PROJECT_DIR/notebooks/<notebook_name>.ipynb")"
BASE="$ORIG_DIR/<notebook_name>"; VERSION=2; OUTPUT="${BASE}_v${VERSION}.ipynb"
while [ -f "$OUTPUT" ]; do VERSION=$((VERSION+1)); OUTPUT="${BASE}_v${VERSION}.ipynb"; done
$PROJECT_DIR/.venv_tools/bin/jupytext --to notebook <notebook_name>.py --output "$OUTPUT"
echo "Saved as: $OUTPUT"

# 11. Cleanup
cd "$PROJECT_DIR" && rm -rf "$TEMP_DIR"
```
