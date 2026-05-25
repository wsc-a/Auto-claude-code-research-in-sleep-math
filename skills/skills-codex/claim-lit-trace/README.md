# claim-lit-trace

`claim-lit-trace` is a Codex CLI skill for tracing the minimal references needed to understand, verify, or use one precise pure-math or representation-theory claim. It is claim-driven: it follows only citations that supply a needed theorem, notation convention, method, hypothesis check, or boundary case, then writes a small reading guide under `docs/claim-lit-traces/<slug>/`.

## When to Use It

Use it when you have a narrow question such as whether a cited theorem applies at the Harish-Chandra module level, whether two papers use compatible Harish-Chandra parameter conventions, or which sources are needed to verify the hypotheses of a branching theorem.

## When Not to Use It

Do not use it for broad related-work search, novelty search, survey writing, proof generation, proof checking, or bibliography-wide citation audit. Use `/research-lit` for broad literature and `/proof-checker` for proof correctness.

## How to Invoke It in Codex CLI

From this repository or a project where ARIS Codex skills are installed:

```text
/skills
# choose claim-lit-trace
Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed. Produce a minimal reading guide.
```

You can also refer to it directly:

```text
/claim-lit-trace "Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed."
```

## Ready-to-Copy First Example

```text
/skills
# choose claim-lit-trace
Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed. Produce a minimal reading guide.
```

## Representation-Theory Example Prompts

```text
Trace the references needed to compare the Harish-Chandra parameter convention in paper A with the LV parameter convention in paper B. Only follow notation or rho-shift sources that directly affect the comparison.
```

```text
Trace only the sources needed to verify the hypotheses of a branching theorem for U(n,1) restricted to U(n-1,1). Separate K-type branching from representation-level branching.
```

```text
Trace the cited references needed to decide whether the passage from smooth representations to K-finite vectors is justified in this argument. Do not trace historical background.
```

## Output Files

Each run creates `docs/claim-lit-traces/<slug>/` with:

- `TARGET_QUESTION.md`: original question, narrowed target, out-of-scope items, and seed papers or locations considered.
- `TARGETED_REFERENCE_TRACE.md`: kept and rejected citation edges, each labeled by role and evidence.
- `MINIMAL_READING_GUIDE.md`: the reading order, exact sections or theorem numbers when known, what to extract, and what to skip.
- `UNRESOLVED_CHECKS.md`: missing PDFs, uncertain theorem numbers, metadata-only references, human-confirmation needs, and representation-theory risks.
- `CLAIM_LIT_TRACE_REPORT.md`: concise final summary, recommended next reading order, and what not to chase further.
- `REFERENCE_TRACE_GRAPH.md`: optional small edge list when useful.

## Evidence Labels

- `CONFIRMED_FROM_TEXT`: Codex inspected the relevant paper text, theorem, definition, remark, or paragraph.
- `CONFIRMED_FROM_CITATION_CONTEXT`: Codex inspected the citing passage and local citation context, but not the full cited source.
- `CONFIRMED_FROM_METADATA`: Codex saw only metadata such as title, abstract, DOI, arXiv ID, venue, or database record.
- `INFERRED_BUT_UNVERIFIED`: the role is plausible but not verified.
- `UNVERIFIED`: the source or role could not be verified.

Treat `CONFIRMED_FROM_METADATA`, `INFERRED_BUT_UNVERIFIED`, and `UNVERIFIED` as prompts for human follow-up before relying on the source in a proof or paper.

## Preparing Local Papers

For better results, put PDFs or notes in one of:

```text
papers/
literature/
research-wiki/
docs/
```

Descriptive filenames help. If you have a local `.bib`, `.tex`, or markdown note with theorem numbers and citation contexts, keep it in the repository so the skill can search it before going online. If `pdftotext` is installed, Codex can inspect PDF text more reliably; otherwise it will record PDF extraction limits in `UNRESOLVED_CHECKS.md`.

## Common Failure Modes

- Missing local PDFs: add the paper to `papers/` or provide an arXiv/DOI link, then rerun.
- Metadata-only result: supply a PDF or exact source text if the theorem role matters.
- Ambiguous theorem number: provide the paper title, edition/version, or page number.
- Broad citation chain: the skill will stop at background citations unless you explicitly ask to trace them.
- Parameter-convention uncertainty: check `UNRESOLVED_CHECKS.md` before using the result.

## Limitations

`claim-lit-trace` does not prove the theorem, repair a proof, or certify that a cited theorem applies. It produces a minimal evidence trail and reading guide. It intentionally refuses broad background expansion unless you explicitly ask for that expansion.
