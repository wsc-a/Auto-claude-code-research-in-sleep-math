# claim-lit-trace

`claim-lit-trace` is a targeted Codex CLI skill for pure-math and representation-theory reference tracing. Given one precise claim or theorem-use question, it follows only the citations needed to understand, verify, or use that claim, then writes a minimal reading guide.

The Codex-discoverable skill lives at:

```text
skills/skills-codex/claim-lit-trace/SKILL.md
```

For consistency with this repository's mirrored skill layout, there is also a mainline copy at:

```text
skills/claim-lit-trace/SKILL.md
```

## How It Differs From research-lit

`/research-lit` is for finding and summarizing related papers on a topic. `claim-lit-trace` is narrower: it starts from one claim and follows only citations that are directly needed for that claim.

It keeps citation edges only when they supply:

- a theorem, proposition, lemma, corollary, or definition directly used by the claim;
- a notation or parameter convention needed to parse the claim;
- a method used in the current proof step;
- a hypothesis check for an invoked theorem;
- a boundary case or exception that directly changes the claim.

It rejects citations that are only historical, broad background, survey context, broad conjecture source, related-work padding, or a much larger theory not needed for the current target.

## When to Use It

Use it for questions like:

- "Which cited sources do I need to read to check this theorem application?"
- "Where is the parameter convention used in this claim coming from?"
- "Which references are necessary to verify the hypotheses of this branching theorem?"
- "Is a citation used directly, or only after an extra representation-theoretic argument?"

## When Not to Use It

Do not use it for:

- broad literature surveys;
- novelty checks;
- proof writing or proof checking;
- bibliography-wide citation audits;
- general historical background;
- "find all papers about X" tasks.

Use `/research-lit` for broad search, `/novelty-check` for novelty, `/proof-checker` for proof verification, and `/citation-audit` for full-paper bibliography integrity.

## Invoke From Codex CLI

If ARIS Codex skills are installed in your project, run:

```text
/skills
# choose claim-lit-trace
Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed. Produce a minimal reading guide.
```

Direct invocation also works when the skill is available:

```text
/claim-lit-trace "Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed."
```

If the skill is not visible in `/skills`, reconcile the Codex skill install:

```bash
bash tools/install_aris_codex.sh . --reconcile
```

## Expected Outputs

Every run creates:

```text
docs/claim-lit-traces/<slug>/
```

with:

- `TARGET_QUESTION.md`: original question, narrowed target, out-of-scope items, seed papers or seed locations considered.
- `TARGETED_REFERENCE_TRACE.md`: target-specific citation chain, kept and rejected edges, edge roles, evidence labels, and reasons.
- `MINIMAL_READING_GUIDE.md`: what to read, what to extract, what to skip, and whether each kept source is necessary, optional, or unresolved.
- `UNRESOLVED_CHECKS.md`: missing PDFs, uncertain theorem numbers, metadata-only references, human-confirmation needs, and representation-theory risks.
- `CLAIM_LIT_TRACE_REPORT.md`: concise final summary, next reading order, and what not to chase further.
- `REFERENCE_TRACE_GRAPH.md`: optional small edge list when useful.

## Evidence Labels

- `CONFIRMED_FROM_TEXT`: Codex inspected the relevant source text.
- `CONFIRMED_FROM_CITATION_CONTEXT`: Codex inspected the citing passage but not the full cited source.
- `CONFIRMED_FROM_METADATA`: Codex verified metadata only.
- `INFERRED_BUT_UNVERIFIED`: plausible but not verified.
- `UNVERIFIED`: could not verify.

The skill must not claim to have read a paper when it only saw metadata.

## Representation-Theory Checks

For kept sources, the skill explicitly checks:

- smooth representation vs Harish-Chandra module vs `(g,K)`-module vs K-finite vectors;
- globalization or K-finite-vector passage;
- Harish-Chandra parameter conventions;
- rho shifts;
- positive-root choices;
- dominance, integrality, and regularity assumptions;
- K-type branching vs representation-level branching;
- whether a theorem is used directly or only after an extra argument.

## Example Prompts

```text
Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Do not trace broad GGP background unless it is directly needed.
```

```text
Trace the references needed to compare the Harish-Chandra parameter convention in paper A with the LV parameter convention in paper B.
```

```text
Trace only the sources needed to verify the hypotheses of a branching theorem for U(n,1) restricted to U(n-1,1).
```

```text
Trace the citation chain needed to determine whether this K-type branching statement implies the claimed representation-level branching statement, and mark any extra argument required.
```

## Limitations

`claim-lit-trace` does not prove the target theorem, repair a proof, or certify theorem applicability. It produces a minimal evidence trail and reading guide. It intentionally refuses broad background expansion unless the user explicitly asks for it.
