# claim-lit-trace Smoke Test

This is a lightweight offline smoke-test scenario for the `claim-lit-trace` skill. It does not require network access.

## Sample User Query

```text
Trace only the references needed to check whether Mock Paper A's Theorem 3.2 can be used at the Harish-Chandra module level. Use local papers first. Do not trace broad GGP background unless directly needed.
```

## Expected Run Directory

The skill should create a directory like:

```text
docs/claim-lit-traces/mock-paper-a-theorem-3-2-hc-module/
├── TARGET_QUESTION.md
├── TARGETED_REFERENCE_TRACE.md
├── MINIMAL_READING_GUIDE.md
├── UNRESOLVED_CHECKS.md
├── CLAIM_LIT_TRACE_REPORT.md
└── REFERENCE_TRACE_GRAPH.md        # optional
```

## Mock Citation Scenario

Assume local notes contain:

```text
Mock Paper A, Theorem 3.2:
  A branching statement is proved for smooth admissible representations.
  The proof says that the Harish-Chandra-module statement follows by taking
  K-finite vectors, citing Mock Paper B, Proposition 2.4.
  For historical background on Gross-Prasad conjectures, see Mock Paper C.

Mock Paper B, Proposition 2.4:
  Under finite length and admissibility assumptions, the K-finite-vector
  functor is compatible with the restriction step used in Paper A.

Mock Paper C:
  Survey of the broad Gross-Prasad program. It does not state the
  K-finite-vector passage used by Paper A.
```

Expected classifications:

- Keep `Mock Paper A -> Mock Paper B, Proposition 2.4` as `HYPOTHESIS_SOURCE` because it directly justifies the Harish-Chandra-module passage.
- Reject `Mock Paper A -> Mock Paper C` as `BACKGROUND_DO_NOT_TRACE` because it is broad historical background and not needed for the target claim.

## Example TARGET_QUESTION.md Snippet

```markdown
# Target Question

## Original User Question
Trace only the references needed to check whether Mock Paper A's Theorem 3.2 can be used at the Harish-Chandra module level.

## Narrowed Target Question
Determine whether the smooth-representation statement in Mock Paper A, Theorem 3.2, can be transferred to the Harish-Chandra-module level via the cited K-finite-vector passage.

## Out of Scope
- General Gross-Prasad history.
- Broad branching-law surveys.
- Proof repair for Theorem 3.2.

## Seed Papers or Locations Considered
- Mock Paper A, Theorem 3.2.
- Mock Paper B, Proposition 2.4.
- Mock Paper C, cited as background only.
```

## Example TARGETED_REFERENCE_TRACE.md Snippet

```markdown
# Targeted Reference Trace

## Kept Edges

| Edge | Label | Evidence | Why Needed |
|---|---|---|---|
| Mock Paper A, Theorem 3.2 -> Mock Paper B, Proposition 2.4 | HYPOTHESIS_SOURCE | CONFIRMED_FROM_CITATION_CONTEXT | Paper A uses this proposition to justify the K-finite-vector passage needed for the Harish-Chandra-module claim. |

## Rejected Edges

| Edge | Label | Evidence | Why Not Traced |
|---|---|---|---|
| Mock Paper A -> Mock Paper C | BACKGROUND_DO_NOT_TRACE | CONFIRMED_FROM_CITATION_CONTEXT | The citation is broad Gross-Prasad background and does not supply the K-finite-vector passage, theorem hypothesis, notation convention, method, or boundary case needed for the target claim. |
```

## Example MINIMAL_READING_GUIDE.md Snippet

```markdown
# Minimal Reading Guide

## 1. Mock Paper A, Theorem 3.2

- Status: necessary
- Evidence: CONFIRMED_FROM_CITATION_CONTEXT
- Read: statement of Theorem 3.2 and the paragraph invoking K-finite vectors.
- Extract: whether the theorem is stated for smooth representations, Harish-Chandra modules, `(g,K)`-modules, or K-finite vectors.
- Skip: introduction and broad Gross-Prasad background.

## 2. Mock Paper B, Proposition 2.4

- Status: necessary
- Evidence: CONFIRMED_FROM_CITATION_CONTEXT
- Read: Proposition 2.4 and its hypotheses.
- Extract: finite-length, admissibility, and K-finite-vector compatibility assumptions.
- Skip: unrelated examples after the proposition unless a hypothesis fails.
```

## Expected Representation-Theory Checks

The run should explicitly record:

- whether Paper A is using smooth representations, Harish-Chandra modules, `(g,K)`-modules, or K-finite vectors;
- whether an extra globalization or K-finite-vector passage is required;
- whether Paper B's hypotheses are verified in Paper A;
- whether GGP background is rejected unless the user asks for original conjectural formulation.
