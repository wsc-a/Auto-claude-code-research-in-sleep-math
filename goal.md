/goal Make a directly usable Codex CLI skill named “claim-lit-trace” inside this ARIS repository.

Goal:
Add a small, directly usable, Codex-compatible skill for targeted pure-math / representation-theory literature tracing.

This should not be a general related-work search tool. It should be a claim-driven reference tracing tool: given one precise mathematical question or claim, it traces only the cited references that are necessary to understand, verify, or use that claim, and then produces a minimal reading guide.

The final result must be usable immediately from Codex CLI through the normal skill mechanism, without requiring me to manually rewrite prompts or inspect the implementation.

First inspect:
- README.md
- AGENT_GUIDE.md
- docs/SKILLS_CATALOG.md if present
- skills/research-lit/SKILL.md if present
- skills/arxiv/SKILL.md if present
- skills/openalex/SKILL.md if present
- skills/semantic-scholar/SKILL.md if present
- skills/citation-audit/SKILL.md if present
- skills/shared-references/ if present
- tools/openalex_fetch.py if present
- tools/semantic_scholar_fetch.py if present
- skills/skills-codex/ if present

Placement requirement:
The main deliverable must be a Codex-discoverable skill.

Preferred path:
- skills/skills-codex/claim-lit-trace/SKILL.md

If this repository uses a different Codex skill layout, adapt to the existing layout and document the decision. Do not put the main skill only in docs. It must live in the Codex-compatible skills directory.

Do not rewrite the whole ARIS system.
Do not create a full Math-ARIS project.
Do not delete or rename existing skills.
Do not substantially rewrite research-lit, arxiv, openalex, semantic-scholar, or citation-audit.
Prefer a small wrapper skill that reuses the existing literature-search tools and adds a narrower claim-driven workflow.

Skill behavior:
The new claim-lit-trace skill must do the following when invoked:

1. Restate and narrow the target question.
   It must create a precise TARGET_QUESTION.md and explicitly say what is out of scope.

2. Search for seed papers or seed locations.
   It should search local papers/literature first if available, then use existing ARIS literature tools such as research-lit, arxiv, OpenAlex, Semantic Scholar, or web search if available.
   It should not prioritize recency by default. For pure mathematics, foundational and exact theorem sources are often more important than recent papers.

3. Apply a strict relevance gate before following any citation.
   The skill must follow a cited reference only if it directly helps answer the current question by providing:
   - a theorem/proposition/lemma directly used for the claim;
   - a notation or parameter convention needed for the claim;
   - a method used in the current proof step;
   - a justification that the hypotheses of a theorem are satisfied;
   - a boundary case or exception directly affecting the claim.

4. Refuse broad background expansion.
   The skill must not trace references that are only:
   - historical origin;
   - general background;
   - survey context;
   - broad conjecture source;
   - “for related work, see ...” citation;
   - a much larger surrounding theory not needed for the current claim.

   Example rule:
   If the user asks about a local branching-law or Harish-Chandra-module-level issue, do not automatically trace back to the original Gross–Prasad / GGP papers unless the user explicitly asks about the original conjectural formulation.

5. Produce a minimal reading guide.
   For each kept paper, the skill must say:
   - why this paper appears;
   - which exact section/theorem/proposition/definition/remark/paragraph to read, if identifiable;
   - what to extract from that part;
   - what can be skipped;
   - whether the paper is necessary, optional, or unresolved for the current claim.

6. Use evidence-honesty labels.
   Each role assignment or recommendation must be labeled:
   - CONFIRMED_FROM_TEXT
   - CONFIRMED_FROM_CITATION_CONTEXT
   - CONFIRMED_FROM_METADATA
   - INFERRED_BUT_UNVERIFIED
   - UNVERIFIED

   The skill must not pretend to have read a paper if it only saw metadata.

7. Include representation-theory-specific checks.
   The skill must explicitly check for:
   - smooth representation vs Harish-Chandra module vs (g,K)-module vs K-finite vectors;
   - globalization or K-finite-vector passage;
   - Harish-Chandra parameter conventions;
   - rho shifts;
   - positive-root choices;
   - dominance, integrality, and regularity assumptions;
   - K-type branching vs representation-level branching;
   - whether a cited theorem is being used directly or only after an extra argument.

Required run output:
When the skill is used, it should create a run directory:

- docs/claim-lit-traces/<slug>/

Inside the run directory, it should create:

1. TARGET_QUESTION.md
   - original user question;
   - narrowed target question;
   - explicit out-of-scope items;
   - seed papers or seed locations considered.

2. TARGETED_REFERENCE_TRACE.md
   - only the relevant citation chain;
   - each edge labeled as one of:
     THEOREM_SOURCE,
     NOTATION_SOURCE,
     METHOD_SOURCE,
     HYPOTHESIS_SOURCE,
     BOUNDARY_CASE_SOURCE,
     BACKGROUND_DO_NOT_TRACE,
     UNRESOLVED;
   - reason why each kept citation is needed;
   - reason why each rejected citation is not traced.

3. MINIMAL_READING_GUIDE.md
   For each kept paper:
   - why it appears;
   - what exact part to read;
   - what to extract;
   - what to skip;
   - necessary / optional / unresolved status.

4. UNRESOLVED_CHECKS.md
   - missing PDFs;
   - uncertain theorem numbers;
   - metadata-only references;
   - references requiring human confirmation;
   - possible representation-level or parameter-convention risks.

5. CLAIM_LIT_TRACE_REPORT.md
   - concise final summary;
   - recommended next reading order;
   - what not to chase further unless explicitly asked.

Optional:
If easy, also produce:
- REFERENCE_TRACE_GRAPH.md
or
- REFERENCE_TRACE_GRAPH.json

But the graph must stay small and target-specific. It must not become a general related-work graph.

Documentation requirement:
Create:
- docs/claim-lit-trace.md

This documentation must explain:
- what claim-lit-trace is;
- how it differs from research-lit;
- when to use it;
- when not to use it;
- how to invoke it from Codex CLI;
- expected outputs;
- limitations;
- at least three example prompts.

Example prompts must include representation-theory examples, such as:
1. “Trace only the references needed to check whether Kobayashi’s Theorem 8.11 can be used at the Harish-Chandra module level. Do not trace broad GGP background unless it is directly needed.”
2. “Trace the references needed to compare the Harish-Chandra parameter convention in paper A with the LV parameter convention in paper B.”
3. “Trace only the sources needed to verify the hypotheses of a branching theorem for U(n,1) restricted to U(n-1,1).”

User README requirement:
After the implementation is complete and verified, create a user-facing README file that tells me exactly how to use the new skill.

Preferred path:
- skills/skills-codex/claim-lit-trace/README.md

If the repository convention suggests a better location, choose that location and document the decision. But the README must be easy to find from the skill directory.

The README must be written for an end user, not for the implementer. It should include:
- what claim-lit-trace does in one paragraph;
- when I should use it;
- when I should not use it;
- how to invoke it in Codex CLI;
- a ready-to-copy first example;
- two or three representation-theory example prompts;
- what output files will be created;
- what each output file means;
- how to interpret evidence labels such as CONFIRMED_FROM_TEXT, CONFIRMED_FROM_METADATA, and UNVERIFIED;
- how to prepare local PDFs or papers if I want better results;
- common failure modes and what to do;
- limitations, especially that it does not prove the theorem and does not chase broad background unless explicitly asked.

The README should be written after the skill is implemented, so that it accurately reflects the final file paths, artifact names, and usage instructions.

Immediate usability requirement:
Add a short usage section that tells me exactly how to use the skill after this goal completes.

It should include a ready-to-copy example such as:

/skills
# choose claim-lit-trace
Trace only the references needed to check whether Kobayashi’s Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed. Produce a minimal reading guide.

Self-test requirement:
Create a lightweight smoke-test example that does not require network access.

Add:
- docs/claim-lit-trace-smoke-test.md

The smoke test should show:
- a sample user query;
- the expected run directory structure;
- a small mock citation scenario;
- the expected classification of at least one kept citation and one rejected broad-background citation;
- example output snippets for TARGET_QUESTION.md, TARGETED_REFERENCE_TRACE.md, and MINIMAL_READING_GUIDE.md.

If the repository has a convention for examples or tests, follow it. Otherwise, the smoke-test markdown file is enough.

Helper scripts:
If there is an existing OpenAlex or Semantic Scholar helper and it is straightforward to extend it to fetch referenced works, you may add a small conservative extension.
But this is optional. Do not make the skill depend on a new helper unless you also verify it.
If you modify any Python file, run a syntax check on modified Python files.

Catalog / discoverability:
If the repository has a skills catalog or Codex skill index that should mention new skills, add a small entry for claim-lit-trace.
If no such catalog exists, do not invent a complex registry; just document the path in docs/claim-lit-trace.md, in the skill README, and in the final report.

Verification before finishing:
Before declaring success, verify all of the following:

- skills/skills-codex/claim-lit-trace/SKILL.md exists, or the closest correct Codex-compatible path exists and is documented.
- The SKILL.md includes:
  - purpose;
  - when to use;
  - when not to use;
  - inputs;
  - workflow;
  - relevance gate;
  - broad-background stop rules;
  - required output artifacts;
  - minimal reading guide rules;
  - evidence-honesty labels;
  - representation-theory-specific checks;
  - final response format.
- docs/claim-lit-trace.md exists.
- docs/claim-lit-trace-smoke-test.md exists.
- A user-facing README exists, preferably at skills/skills-codex/claim-lit-trace/README.md, and it explains how to use the skill immediately from Codex CLI.
- If a catalog exists and is appropriate to update, it mentions claim-lit-trace.
- No existing ARIS skill was deleted or substantially rewritten.
- Any modified Python file passes a basic syntax check.
- The final documentation tells me exactly how to invoke the skill in Codex CLI.

Final implementation report:
Create:
- docs/CLAIM_LIT_TRACE_IMPLEMENTATION_REPORT.md

The report must include:
- files created;
- files modified;
- verification checklist with pass/fail;
- how to use the skill immediately;
- location of the user-facing README and a brief summary of what it covers;
- limitations;
- what was intentionally not implemented;
- next steps.

Definition of done:
This goal is complete only when I can open Codex CLI in this repository, choose or refer to the claim-lit-trace skill, paste a representation-theory question, and get the required run artifacts without needing to manually edit the skill or invent a new prompt.

Stop condition:
Stop after claim-lit-trace is implemented, documented, smoke-tested, reported, and the user-facing README is written. Do not expand into proof-writing, proof-checking, paper-writing, Monap integration, or a full Math-ARIS pipeline.