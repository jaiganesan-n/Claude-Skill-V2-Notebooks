# Notebook Update Guidelines
# Full Stack AI Engineering Course — notebook-edit reference

Read this file before making any edits to a course notebook.
Work through each section as a checklist. Document findings in your final update report.

---

## How to Use This File

1. Identify the notebook's chapter from `course_syllabus.md`
2. Run all **General Update Guidelines** (G1–G5) — apply to every edit session
3. Run all **Pre-Edit Checks** (P1–P6) — apply to every notebook
4. Run the **Content & Concept Checks** relevant to this chapter's topic
5. Apply your changes — minimal and focused
6. Complete the **Post-Edit Verification** before converting back to `.ipynb`
7. Complete all **Must-Do Guidelines** (M1) after a successful edit

If a check finds nothing to change, note it as "checked — no issues." Never skip silently.

---

## General Update Guidelines

These run on every edit session before touching any code.

### G1 — Web Search for Latest Implementation Patterns

Before making any edits, search for recent changes to the major libraries used in the notebook (e.g. `anthropic`, `langchain`, `llama-index`, `openai`, `gradio`, `transformers`). Look for:

- Deprecated methods or changed import paths since the notebook was last updated
- New recommended patterns for SDK initialization, API calls, or tool use
- Current stable version numbers for the packages being installed

Document what you find. If the search reveals a breaking change or a clearly better pattern, apply it as part of the edit. If it would change the core flow, flag it to the user first.

### G2 — Notebook Readability & Purpose Header

Before making code changes, read the first few cells of the notebook. Verify it opens with a clear purpose header containing:

| Element | Description |
|---|---|
| Title | What the notebook is about |
| Content overview | The concepts or techniques demonstrated |
| Learning objectives | What the learner will be able to do after completing it |
| Prerequisites | What prior knowledge is assumed |
| What's New | Targeted update notes — present only in updated versions (v2+) |

If any element is missing or vague, add or improve it as part of the edit. This counts as a P6-level fix and must be noted in the update report under Changes Made.

### G3 — Package Version Check & Update

After installing the notebook's existing dependencies, check whether any pinned packages have newer stable versions available:

```bash
venv_notebook/bin/python -m pip list --outdated --format=columns 2>/dev/null | tee outdated_packages.txt
cat outdated_packages.txt
```

For each outdated package:

1. Check `$PROJECT_DIR/.claude/skills/notebook-create/colab_libraries.txt` — if Colab pins an older version of that package or a dependency, do **not** upgrade beyond what Colab supports.
2. If upgrading is safe (no Colab conflict), bump the version in `requirements.txt` and re-run the install to verify no new conflicts appear.
3. After resolving, re-run the full dependency validation workflow (see `NOTEBOOK_WORKFLOW.md`) to confirm pinned versions are consistent.
4. Update the `!pip install` line in the `.py` file with the final validated versions.

Document every version change (or decision not to change) in the update report under P3.

### G4 — .env Verification Before Testing

Before running the notebook script, read `$PROJECT_DIR/.env` (if it exists) and cross-check it against the API keys the notebook needs. Identify what keys the notebook imports (e.g. `GOOGLE_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `TOGETHER_API_KEY`) and confirm each is present.

```bash
# Show which keys are defined in .env (values masked)
if [ -f "$PROJECT_DIR/.env" ]; then
  grep -v '^#' "$PROJECT_DIR/.env" | grep '=' | sed 's/=.*/=<set>/' | sort
else
  echo "No .env file found at $PROJECT_DIR/.env"
fi
```

If a key is missing:
- Do **not** hardcode it — stop and inform the user which key is missing and how to add it to `.env`.
- If `.env` does not exist at all, warn the user and note that tests will fail for any notebook that requires API credentials.

### G5 — Read `update_log.json` Before Editing

Check if `$PROJECT_DIR/update_log.json` exists. If it does, read it and review which targeted additions have already been logged for other notebooks. Use this to avoid introducing the same specific addition (e.g. a widget, an optional pipeline cell, a new section) into multiple notebooks redundantly.

The log only tracks targeted/specific additions. General updates (dep versions, model names, API patterns, typo fixes) are never logged there.

---

## Pre-Edit Checks

These run on every notebook, regardless of chapter.

### P1 — Model Names

Scan every string that references an LLM or embedding model by name.

- Are the model names still actively supported by the provider?
- Are there newer, cost-equivalent models the course should prefer?
- Are any deprecated or retired model names still being referenced?
- Are embedding model names current? (Embedding models also get deprecated.)

When in doubt, check the provider's official models page before assuming a model is still valid.

### P2 — API Patterns

Scan for how the notebook calls external APIs.

- Is the SDK initialization pattern current? (e.g., how the client object is created)
- Are deprecated endpoint methods still being used?
- Does the notebook use the latest response-parsing patterns for that SDK version?
- Are there newer, cleaner ways to accomplish the same API call that the provider now recommends?

### P3 — Dependency Versions

Check the pip install cell and all imports.

- Is every imported package listed in the `!pip install` cell with a pinned version?
- Are the pinned versions still compatible with each other and with Colab's environment?
- Have any packages been renamed, split into sub-packages, or had their install names changed?
- Are there packages being installed that are no longer needed?

### P4 — API Key Handling

Check how credentials are loaded.

- Are API keys hardcoded anywhere in the notebook? (Never acceptable.)
- Does the key-loading approach work in both Colab and local environments?
- Are environment variable names consistent with what the course `.env` template expects?

### P5 — Python & Runtime Compatibility

- Does the notebook assume a Python version that has since changed compatibility requirements?
- Does it run correctly in Colab's current default runtime?
- If a GPU is required, is that disclosed clearly near the top of the notebook?

### P6 — Grammar, Typos, and Learner-Facing Text

Scan and flag grammar issues and typos in:
- Markdown explanation cells
- Print statements and output messages visible to learners
- Instructional comments inside code cells
- Docstrings on functions defined in the notebook

Do not attempt fixes during this pre-edit check — the `.py` file may not exist yet. Carry flagged issues into Step 4 (Make Edits) and fix them alongside the requested changes. Do not change variable names, logic, or code style under this rule.

---

## Content & Concept Checks

Beyond syntax and API calls, check whether the approach being taught is still current best practice.

### C1 — Is the Approach Still Recommended?

For every major technique demonstrated in the notebook, ask:

- Has the field moved on from this approach?
- Is there now a simpler or more reliable way to accomplish the same result?
- Would a learner following this notebook in the current year be learning the right way to do this?

If the approach is intentionally simplified for learning purposes, that is fine — just make sure the explanation reflects that.

### C2 — Framework & Library Patterns

When the notebook uses a framework (LangChain, LlamaIndex, HuggingFace, etc.):

- Are the import paths still valid, or has the library reorganized its package structure?
- Have any core abstractions used in this notebook been deprecated or renamed?
- Has the recommended way to initialize or configure the framework changed?
- Are there new built-in features that replace patterns the notebook implements manually?

Flag anything that would produce a deprecation warning or error when a learner runs the notebook today.

### C3 — RAG-Specific Checks (Chapters 2–6, 8)

When the notebook covers retrieval or RAG:

- Is the chunking and indexing approach consistent with current best practice?
- Does the retrieval step reflect current recommendations (e.g., hybrid search where relevant)?
- Is the evaluation approach shown still representative of how RAG systems are assessed?
- Are the vector database patterns current for the library being used?

### C4 — Fine-Tuning Checks (Chapter 7)

When the notebook covers fine-tuning:

- Is the fine-tuning endpoint or SDK method still the current one from the provider?
- Are the training configuration parameters aligned with current library defaults?
- Is the model being fine-tuned still available and recommended for fine-tuning?
- Are dataset formatting requirements still correct for the current SDK version?

### C5 — Agent & Tool Use Checks (Chapters 8, 10)

When the notebook covers agents:

- Are the agent-building patterns (how tools are defined, how the agent loop runs) still current?
- Have any core agent abstractions been deprecated in favor of newer patterns?
- Is the tool-calling syntax consistent with the current SDK's expectations?
- If the notebook demonstrates multi-step or multi-agent patterns, do those patterns still reflect how the framework recommends building them?

### C6 — Deployment & Integration Checks (Chapter 9)

When the notebook covers APIs, Gradio, or deployment:

- Are the UI component names and event handler patterns current?
- Is the API setup pattern still idiomatic?
- Are the HuggingFace Spaces deployment steps still accurate?

---

## Concept Delivery Check

This is separate from technical correctness. Read through the notebook once as a learner before finishing.

- Does the narrative flow make sense? Does each cell build on the last?
- Are the explanations accurate given what the code now does — especially after updates?
- If you changed an API pattern or approach, did you also update the markdown explanation that describes it?
- Are the learning objectives of the chapter met by what the notebook actually demonstrates?

If a technical update changed what a cell does, the surrounding explanation must reflect that. Never leave explanatory text that contradicts the updated code.

---

## Minimal Change Principle

Apply only what is necessary to fulfill the update request. Do not:

- Refactor code that is not broken
- Add features or demonstrations not in scope
- Restructure notebook sections unless the structure is causing confusion
- Change code style, naming conventions, or patterns that are working correctly

More changes = more risk of introducing new issues. Targeted updates are better updates.

### Core Flow / Pipeline / Idea — Hard Constraint

**Do not change the core flow, pipeline, or central idea of the notebook** during any update or test cycle — unless the user explicitly tells you to.

This constraint applies even when:
- A content check (C1–C6) flags the approach as outdated
- A dependency or API update could be done more cleanly with a different pattern
- Testing reveals an error that could be fixed by restructuring the pipeline

If preserving the core flow conflicts with making a required fix, **flag it to the user** and wait for explicit instruction before changing the flow. Document this in the "Items Skipped" section of the update report with reason: "core flow preserved — user instruction required to change".

---

## Post-Edit Verification

Before converting the `.py` file back to `.ipynb`, confirm:

- [ ] Cell execution order is strictly linear — no cell uses a variable defined in a later cell
- [ ] G1 — web search completed for major libraries in this notebook
- [ ] G2 — notebook purpose header present and complete (title, overview, objectives, prerequisites, What's New if v2+)
- [ ] G3 — outdated packages checked; versions updated or justified under P3
- [ ] G4 — `.env` verified; all required API keys confirmed present
- [ ] G5 — `update_log.json` read before editing
- [ ] P1 — model names: all flagged names resolved or documented in Items Skipped
- [ ] P2 — API patterns: deprecated calls updated or documented in Items Skipped
- [ ] P3 — dependencies: `!pip install` cell complete with pinned versions for every import
- [ ] P4 — API key handling: no hardcoded credentials anywhere in the notebook
- [ ] P5 — runtime compatibility: runs in Colab's current environment; GPU disclosure present if needed
- [ ] P6 — learner-facing text: all flagged grammar issues and typos fixed
- [ ] Markdown explanations are consistent with the code as it now stands
- [ ] M1 — `update_log.json` updated with targeted additions from this session
- [ ] Final update report is complete

---

## Must-Do Guidelines

### M1 — Maintain `update_log.json` After Every Edit

After a successful edit, update `$PROJECT_DIR/update_log.json` with any targeted additions made in this session. This step is **mandatory** — do not skip it.

#### What to log — specific, non-redundant additions only

- New pipeline cells or pipeline variations
- Optional code blocks or alternate implementations
- New widgets, interactive controls, or visualizations
- New content sections or unique demonstrations added to this notebook

#### What NOT to log — general updates are excluded

- Dependency version bumps
- Model name or API pattern updates
- Grammar, typo, or readability fixes
- Structural or formatting changes

#### Entry format

4–5 words maximum per entry. No full sentences.

#### JSON structure

```json
{
  "<notebook_name>": {
    "v2": ["optional reranker cell", "progress widget added"],
    "v3": ["async retrieval option"]
  }
}
```

- Each top-level key is the notebook's base name (no version suffix, no `.ipynb`).
- Each version key (`v2`, `v3`, …) corresponds to the version suffix of the output file. Versioning starts at `v2` — `v1` is the original.
- Each version key lists **only what is new in that version**. Do not copy prior-version entries forward.
- Every new version inherits the content of the original (v1) notebook implicitly.

#### Procedure

1. Check if `$PROJECT_DIR/update_log.json` exists.
   - **Exists:** read it, locate the entry for the current notebook, add the new version key with its entries.
   - **Does not exist:** create it with only the current notebook's entry.
2. If a notebook has been **deleted**, remove its key from the log entirely.
3. Write the updated log back using the Edit or Write tool. **Never overwrite unrelated notebook entries.**

---

## Update Report Template

```
Notebook: <filename>
Chapter: <chapter number and name>
Date: <today>

General Update Guidelines
- G1 Web search findings: [what was found / no breaking changes]
- G2 Readability check: [header elements present / what was added]
- G3 Package versions: [packages updated / no outdated packages]
- G4 .env verification: [keys confirmed / issues found]
- G5 update_log.json read: [reviewed / file not found]

Pre-Edit Checks
- P1 Model names: [findings]
- P2 API patterns: [findings]
- P3 Dependencies: [findings]
- P4 API key handling: [findings]
- P5 Runtime compatibility: [findings]
- P6 Grammar / learner text: [findings]

Content & Concept Checks
- [list which C-sections were relevant and what was found]

Changes Made
- [bullet each change — what was updated and why]

Items Skipped
- [anything flagged but not changed, and the reason]

Must-Do
- M1 update_log.json: [entries added / no targeted additions this session]
```
