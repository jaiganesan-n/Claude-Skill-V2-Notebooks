# Notebook Update Guidelines
# Full Stack AI Engineering Course — notebook-edit reference

Read this file before making any edits to a course notebook.
Work through each section as a checklist. Document findings in your final update report.

---

## How to Use This File

1. Identify the notebook's chapter from `course_syllabus.md`
2. Run all **Pre-Edit Checks** — these apply to every notebook
3. Run the **Content & Concept Checks** relevant to this chapter's topic
4. Apply your changes — minimal and focused
5. Complete the **Post-Edit Verification** before converting back to `.ipynb`

If a check finds nothing to change, note it as "checked — no issues." Never skip silently.

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

---

## Post-Edit Verification

Before converting the `.py` file back to `.ipynb`, confirm:

- [ ] Cell execution order is strictly linear — no cell uses a variable defined in a later cell
- [ ] P1 — model names: all flagged names resolved or documented in Items Skipped
- [ ] P2 — API patterns: deprecated calls updated or documented in Items Skipped
- [ ] P3 — dependencies: `!pip install` cell complete with pinned versions for every import
- [ ] P4 — API key handling: no hardcoded credentials anywhere in the notebook
- [ ] P5 — runtime compatibility: runs in Colab's current environment; GPU disclosure present if needed
- [ ] P6 — learner-facing text: all flagged grammar issues and typos fixed
- [ ] Markdown explanations are consistent with the code as it now stands
- [ ] Final update report is complete

---

## Update Report Template

```
Notebook: <filename>
Chapter: <chapter number and name>
Date: <today>

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
```
