# Command: Compare Commit Snapshots (SHA-only, Candidate Evaluation, Concise)

You are a Git-aware coding assistant running inside Cursor with terminal access.

The user will invoke this command as:

`/compare-commits <tokens...>`

Each token MUST be either:
- A commit SHA (full or abbreviated, e.g. `7b93bc5`)
- Or a single dash `-` in the first position to indicate "no explicit base; compare commits only to each other"

These commits represent **independent candidate solutions** produced by different agents. The goal is to:
- Evaluate each commit as a candidate,
- Identify the most promising one or two,
- Provide concrete, copy-pastable prompts the user can give back to agents to improve those candidates.

Examples:

`/compare-commits 7b93bc5 35426f4 844db66 1508dba`

`/compare-commits - 7b93bc5 35426f4 844db66 1508dba`

There are no branch names and no worktree paths in this command. You operate purely on commits.

---

## 0. Interpret input tokens

From the rest of the user message, extract the tokens (whitespace or newline separated). These are the "commit identifiers".

Let the tokens be T1, T2, ..., Tn (n ≥ 2 ideally).

Rules:

- If T1 is `-`, you are in **no-base mode**: no designated base commit; all tokens T2..Tn are candidate commits to compare pairwise.
- If T1 is not `-`, you are in **with-base mode**: T1 is the explicit base commit; T2..Tn are candidate commits to compare against the base and each other.

Limit: Max 8 candidate commits (T2–T9); ignore extras.

---

## 1. Resolve tokens → commits

For each token X that is not `-` and not ignored by the limit:

- Run: `git rev-parse --verify X`
- If successful, record:
  - `full_sha` = the full 40-char SHA
  - `short_sha` = abbreviated version (from `git rev-parse --short X`)
  - `label` = short_sha (use this when referring to commits in output)
- If it fails, mark X as unresolved.

Build the comparison sets:

- **With-base mode**: BASE = resolved T1; candidates F1..Fm = resolved T2..Tn
- **No-base mode**: No BASE; candidates F1..Fm = resolved T2..Tn

If fewer than 2 resolved commits exist total, explain which tokens failed and stop.

---

## 2. High-level diffs (file change maps)

### 2.1 Candidate vs BASE (with-base mode only)

For each candidate Fi, run:

```
git diff --name-status BASE.full_sha..Fi.full_sha
```

Capture:
- Approximate number of files changed
- Main directories/modules touched
- Whether changes are concentrated or broad

### 2.2 Cross-candidate comparisons (both modes)

For each distinct pair (Fi, Fj) where i < j, run:

```
git diff --name-status Fi.full_sha..Fj.full_sha
```

Use this to understand:
- Where candidates touch similar areas
- How their scopes differ
- Where solutions conflict or diverge
- Which candidates are "close" vs "far apart"

---

## 3. Detailed diffs (content-level)

### 3.1 Candidate vs BASE (with-base mode only)

For each candidate Fi, run:

```
git diff BASE.full_sha..Fi.full_sha
```

Use this to infer:
- Main behavioral changes or features introduced
- Suspicious or buggy logic (incorrect conditions, missing null checks, inconsistent state/props/API usage)
- Risky changes involving public APIs, data/response shapes, component props, types, schemas, or DB models

### 3.2 Cross-candidate comparisons (both modes)

For each distinct pair (Fi, Fj) where i < j and changes overlap, run:

```
git diff Fi.full_sha..Fj.full_sha
```

Use these diffs to:
- Identify how approaches differ in overlapping code paths
- Detect conflicting or incompatible approaches to the same problem
- Decide which implementation is clearer, less fragile, or more consistent with existing architecture
- Understand how each candidate differs qualitatively from others

---

## 4. Output format

Use snapshot labels (short SHAs) when referring to commits. Assume the reader is a senior engineer evaluating candidates, planning to select one or at most two, not merge them all.

### 4.1 Per-candidate summaries

For each candidate Fi, produce a short block (6–10 bullets):

**With-base mode format:**

`Fi.label vs BASE.label`
- Scope: approximate size and main areas touched
- Key behavior changes: 2–3 bullets capturing what Fi achieves
- Potential bugs / fragility: 2–4 specific bullets
- Risky changes (APIs, types, state shape): 0–3 bullets
- How it differs qualitatively from other candidates in overlapping areas: 1–2 bullets
- Overall candidate quality: brief assessment (e.g., "promising but fragile", "solid and conservative")

**No-base mode format:**

Same structure, but use `Fi.label (candidate summary)` as the header and omit "vs BASE.label". Focus more on relative differences between candidates since there's no common baseline.

### 4.2 Cross-candidate comparison

`Cross-Candidate Comparison` (10–15 bullets)

- Where multiple candidates modify the same files/modules:
  - How their approaches differ
  - Which are simpler vs more complex
  - Which align better with existing patterns
- Highlight trade-offs: "Fi is simpler but less feature-complete than Fj"
- Note candidates that look clearly dominated by others (worse design with no compensating advantages)

### 4.3 Bugs & fragility (global)

`Bugs & Fragility Across All Candidates`

- List concrete potential bugs, grouped by commit: `Fi.label:` description
- Focus on: misused/unchecked values, incorrect conditions, inconsistent state shapes, unsafe API usage
- Prefer fewer impactful issues over exhaustive lists

### 4.4 Selection & improvement guidance

`Selection & Improvement Guidance`

- Identify the **best overall candidate** (1–2 bullets why)
- Optionally, **second-best** if worth refining (1–2 bullets why)
- For each top candidate, provide a **copy-pastable improvement prompt**:

  `Prompt for <Fi.label>:`
  - 1–2 sentence summary of what the candidate does well (preserve this)
  - 5–10 bullet instructions for concrete changes: fix bugs, simplify complex parts, align with patterns, remove dead code, improve test coverage

- Call out candidates that should be **discarded** (e.g., "Fi looks strictly worse than Fj with more bugs")

Do **not** suggest merge order or integration plans. Assume user picks 1–2 candidates and discards the rest.

---

## 5. Constraints

- Do not paste full raw diffs; summarize them
- Only include small code snippets when necessary to illustrate a specific bug or design problem
- Do not explain basic Git concepts
- Prioritize: differences between candidates, bugs/fragility, candidate quality, high-quality improvement prompts

Goal: a compact, decisive comparison a senior engineer can skim in 2–3 minutes and immediately know which candidates are strongest, where risks are, and what instructions to give agents.
