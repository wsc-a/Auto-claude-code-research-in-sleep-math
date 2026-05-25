---
name: claim-lit-trace
description: Targeted claim-driven reference tracing for pure mathematics and representation theory. Use when the user gives one precise mathematical claim, theorem-use question, parameter-convention question, or branching-law verification task and wants only the cited references needed to understand, verify, or use that claim. Not for broad related-work search.
argument-hint: [precise-claim-or-question]
allowed-tools: Bash(*), Read, Glob, Grep, WebSearch, WebFetch, Write
---

# Claim Lit Trace

Target question: $ARGUMENTS

## Purpose

Trace a minimal citation chain for one precise mathematical claim. The output is a small run directory with a narrowed question, kept and rejected citation edges, a minimal reading guide, unresolved checks, and a concise final report.

This is a wrapper around existing ARIS literature capabilities (`research-lit`, `arxiv`, `openalex`, `semantic-scholar`, local PDFs, local notes, and web search), but with a stricter claim-level gate. Do not turn the task into a general literature review.

## Inputs

The user should provide one precise mathematical question or claim. Useful optional details are paper title, local file path, arXiv ID, DOI, theorem/proposition number, cited paragraph, group pair, representation category, or parameter convention. If the user provides multiple claims, pick the narrowest one and record the rest as out of scope unless the user explicitly asks for separate runs.

## When to Use

Use this skill when the user asks for:
- the exact references needed to check one theorem-use claim;
- the citation trail needed to compare notation or parameter conventions;
- the sources needed to verify hypotheses of a representation-theoretic theorem;
- a minimal reading order for one local branching-law, Harish-Chandra-module, `(g,K)`-module, K-type, or parameter-convention issue.

## When Not to Use

Do not use this skill for:
- general related work;
- novelty search;
- survey writing;
- proof generation or proof repair;
- bibliography-wide citation audits;
- broad history of a theory;
- finding all papers related to a topic.

If the user asks for broad background, suggest `/research-lit` instead. If the user asks whether a proof is correct, suggest `/proof-checker` instead.

## Required Output Directory

Every invocation must create:

```text
docs/claim-lit-traces/<slug>/
```

Use a short lowercase slug from the target question, for example `kobayashi-theorem-8-11-hc-module`. If the slug already exists, append `-2`, `-3`, etc. Create these files on every run:

```text
TARGET_QUESTION.md
TARGETED_REFERENCE_TRACE.md
MINIMAL_READING_GUIDE.md
UNRESOLVED_CHECKS.md
CLAIM_LIT_TRACE_REPORT.md
```

Optionally create `REFERENCE_TRACE_GRAPH.md` when the graph has at most 12 nodes and remains target-specific.

## Evidence Labels

Every role assignment, recommendation, and reading-guide entry must carry exactly one label:

- `CONFIRMED_FROM_TEXT`: you inspected the relevant paper text, theorem, definition, remark, or paragraph.
- `CONFIRMED_FROM_CITATION_CONTEXT`: you inspected the citing passage and the local citation context, but not the full cited source.
- `CONFIRMED_FROM_METADATA`: you only verified bibliographic metadata, abstract, title, venue, DOI, arXiv ID, or database record.
- `INFERRED_BUT_UNVERIFIED`: the role is plausible from surrounding evidence but has not been verified in the cited text.
- `UNVERIFIED`: you could not verify the source or role.

Never imply that you read a paper if the evidence is only metadata.

## Workflow

### Step 1: Restate and Narrow

Write `TARGET_QUESTION.md` before doing external expansion. Include:

- original user question;
- narrowed target question in one or two sentences;
- assumptions made to make the task tractable;
- explicit out-of-scope items;
- seed papers, theorem numbers, local paths, arXiv IDs, DOI strings, or names already supplied by the user.

If the question is too broad, narrow it to the smallest checkable claim rather than expanding the search. Ask a clarification only when there is no defensible target question.

### Step 2: Search Seed Papers or Seed Locations

Search local material first:

1. User-supplied files, arXiv IDs, DOI strings, theorem names, or paper names.
2. `research-wiki/`, `papers/`, `literature/`, `docs/`, `*.tex`, `*.bib`, and `*.md` files.
3. Local PDFs whose filenames or first pages match the target. Use `pdftotext` if available; if not, record the limitation in `UNRESOLVED_CHECKS.md`.

Then use external sources only as needed:

- `arxiv_fetch.py` for exact arXiv title/author/theorem-source searches.
- `openalex_fetch.py` for open citation metadata and DOI/title disambiguation.
- `semantic_scholar_fetch.py` for venue metadata and single-paper records.
- Web search or WebFetch for exact theorem pages, publisher pages, arXiv abstracts, and PDF text when available.

Do not prioritize recency by default. For pure mathematics, prefer foundational or exact theorem sources when they are the sources actually used by the claim.

### Step 3: Reuse Existing Helpers Conservatively

When using ARIS helper scripts, resolve them through the installed ARIS repository or local helper locations. Use this pattern and substitute the helper name:

```bash
if [ -z "${ARIS_REPO:-}" ]; then
  for manifest in .aris/installed-skills-codex.txt .aris/installed-skills.txt; do
    [ -f "$manifest" ] && ARIS_REPO=$(awk -F'\t' '$1=="repo_root"{print $2; exit}' "$manifest" 2>/dev/null) && break
  done
fi
HELPER=""
[ -n "${ARIS_REPO:-}" ] && [ -f "$ARIS_REPO/tools/HELPER_NAME.py" ] && HELPER="$ARIS_REPO/tools/HELPER_NAME.py"
[ -z "$HELPER" ] && [ -f ".aris/tools/HELPER_NAME.py" ] && HELPER=".aris/tools/HELPER_NAME.py"
[ -z "$HELPER" ] && [ -f "tools/HELPER_NAME.py" ] && HELPER="tools/HELPER_NAME.py"
```

Use helper output as metadata unless you also inspect the relevant paper text. Keep external searches narrow: exact theorem number, author pair, representation name, group pair, or parameter convention.

### Step 4: Strict Relevance Gate

Before following any cited reference, classify the edge. Follow a citation only if it directly helps answer the current target question as one of:

- `THEOREM_SOURCE`: theorem, proposition, lemma, corollary, or definition directly used for the claim.
- `NOTATION_SOURCE`: notation or parameter convention needed to parse the claim.
- `METHOD_SOURCE`: proof method used in the current proof step.
- `HYPOTHESIS_SOURCE`: justification that hypotheses of a theorem are satisfied.
- `BOUNDARY_CASE_SOURCE`: exception, endpoint, singular case, reducibility issue, nonregular case, or other boundary condition that directly affects the claim.

Reject and stop tracing an edge as `BACKGROUND_DO_NOT_TRACE` when it is only:

- historical origin;
- general background;
- survey context;
- broad conjecture source;
- "for related work, see ..." citation;
- a much larger surrounding theory not needed for the current claim.

Use `UNRESOLVED` when a citation might be necessary but the needed source text, theorem number, or citation context cannot be verified.

Example stop rule: if the target is a local branching-law or Harish-Chandra-module-level issue, do not trace to original Gross-Prasad or GGP papers unless the user explicitly asks about the original conjectural formulation or those papers directly provide a needed theorem, notation, or hypothesis.

### Step 5: Representation-Theory Checks

For every kept source and every theorem-use recommendation, explicitly check:

- smooth representation vs Harish-Chandra module vs `(g,K)`-module vs K-finite vectors;
- whether a globalization or K-finite-vector passage is needed;
- Harish-Chandra parameter convention;
- rho shifts;
- positive-root choices;
- dominance, integrality, and regularity assumptions;
- K-type branching vs representation-level branching;
- whether the cited theorem is used directly or only after an extra argument.

If a check is irrelevant, say `not applicable`. If it cannot be verified, put it in `UNRESOLVED_CHECKS.md`.

### Step 6: Write Required Artifacts

`TARGET_QUESTION.md` must include:

- original user question;
- narrowed target question;
- explicit out-of-scope items;
- seed papers or seed locations considered.

`TARGETED_REFERENCE_TRACE.md` must include:

- only the relevant citation chain;
- each kept or rejected edge labeled `THEOREM_SOURCE`, `NOTATION_SOURCE`, `METHOD_SOURCE`, `HYPOTHESIS_SOURCE`, `BOUNDARY_CASE_SOURCE`, `BACKGROUND_DO_NOT_TRACE`, or `UNRESOLVED`;
- evidence label for each edge;
- reason why each kept citation is needed;
- reason why each rejected citation is not traced.

`MINIMAL_READING_GUIDE.md` must include, for each kept paper:

- why this paper appears;
- exact section, theorem, proposition, definition, remark, or paragraph to read if identifiable;
- what to extract;
- what to skip;
- status: `necessary`, `optional`, or `unresolved`;
- evidence label.

`UNRESOLVED_CHECKS.md` must include:

- missing PDFs;
- uncertain theorem numbers;
- metadata-only references;
- references requiring human confirmation;
- possible representation-level or parameter-convention risks.

`CLAIM_LIT_TRACE_REPORT.md` must include:

- concise final summary;
- recommended next reading order;
- what not to chase further unless explicitly asked;
- list of files written.

### Step 7: Optional Small Graph

If useful and small, write `REFERENCE_TRACE_GRAPH.md` with a simple edge list:

```text
target claim -> Paper A, Theorem X [THEOREM_SOURCE, CONFIRMED_FROM_TEXT]
Paper A -> Paper B, Definition Y [NOTATION_SOURCE, CONFIRMED_FROM_CITATION_CONTEXT]
Paper A -> Paper C [BACKGROUND_DO_NOT_TRACE, CONFIRMED_FROM_CITATION_CONTEXT]
```

Do not include broad related-work nodes.

## Final Response Format

End with:

```text
Created claim-lit-trace run: docs/claim-lit-traces/<slug>/

Read first:
1. <paper/section/theorem> - <reason>
2. <paper/section/theorem> - <reason>

Do not chase further unless explicitly needed:
- <background edge> - <reason>

Open checks:
- <missing/uncertain item or "none">
```

Do not paste the full artifact contents into the chat unless the user asks. The files are the source of truth.
