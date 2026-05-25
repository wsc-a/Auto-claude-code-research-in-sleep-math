# claim-lit-trace Implementation Report

## Files Created

- `skills/skills-codex/claim-lit-trace/SKILL.md`
- `skills/skills-codex/claim-lit-trace/README.md`
- `skills/claim-lit-trace/SKILL.md`
- `docs/claim-lit-trace.md`
- `docs/claim-lit-trace-smoke-test.md`
- `docs/CLAIM_LIT_TRACE_IMPLEMENTATION_REPORT.md`

## Files Modified

- `docs/SKILLS_CATALOG.md`
- `skills/skills-codex/README.md`

## Verification Checklist

| Requirement | Status | Evidence |
|---|---|---|
| Codex-discoverable skill exists | PASS | `skills/skills-codex/claim-lit-trace/SKILL.md` |
| Main deliverable is in the Codex skill layout | PASS | `skills/skills-codex/claim-lit-trace/` follows the existing Codex mirror path |
| Mainline mirror exists for repository convention | PASS | `skills/claim-lit-trace/SKILL.md` |
| Skill includes purpose | PASS | `## Purpose` in `SKILL.md` |
| Skill includes when to use / when not to use | PASS | `## When to Use`, `## When Not to Use` |
| Skill includes inputs | PASS | `## Inputs` |
| Skill includes workflow | PASS | `## Workflow` |
| Skill includes strict relevance gate | PASS | `### Step 4: Strict Relevance Gate` |
| Skill includes broad-background stop rules | PASS | `BACKGROUND_DO_NOT_TRACE` rules and GGP stop example |
| Skill includes required output artifacts | PASS | `## Required Output Directory` and Step 6 |
| Skill includes minimal reading guide rules | PASS | `MINIMAL_READING_GUIDE.md` requirements in Step 6 |
| Skill includes evidence-honesty labels | PASS | `## Evidence Labels` |
| Skill includes representation-theory checks | PASS | `### Step 5: Representation-Theory Checks` |
| Skill includes final response format | PASS | `## Final Response Format` |
| Documentation page exists | PASS | `docs/claim-lit-trace.md` |
| Offline smoke-test example exists | PASS | `docs/claim-lit-trace-smoke-test.md` |
| User-facing README exists | PASS | `skills/skills-codex/claim-lit-trace/README.md` |
| Catalog or index mentions the skill | PASS | `docs/SKILLS_CATALOG.md` and `skills/skills-codex/README.md` mention `claim-lit-trace` |
| No existing ARIS skill was deleted or substantially rewritten | PASS | Only new skill directories were added; existing literature skills were not edited |
| Modified Python files pass syntax check | PASS | No Python files were modified; syntax check not applicable |
| Final documentation tells how to invoke from Codex CLI | PASS | `docs/claim-lit-trace.md` and the skill README include `/skills` and `/claim-lit-trace` examples |

## Verification Commands Run

- Targeted file existence checks for the new skill, docs, smoke test, README, and report: PASS.
- Targeted `rg` checks for required `SKILL.md` sections, output artifact names, evidence labels, representation-theory checks, and catalog/index mentions: PASS.
- `diff -q skills/claim-lit-trace/SKILL.md skills/skills-codex/claim-lit-trace/SKILL.md`: PASS.
- Forbidden Codex reviewer/helper-pattern check on the new Codex skill (`mcp__codex__codex`, `codex-reply`, `threadId`, direct `python3 tools/` style calls): PASS.
- `git diff --check`: PASS.
- `python tools/check_skills_inventory.py`: FAIL, because the repository already has broader inventory drift (`wiki-enrich` lacks a Codex mirror/catalog row) and global count strings still report `76` while the current tree now contains additional skills. This implementation added `claim-lit-trace` to the Codex skill path, mainline skill path, skill catalog, and Codex README, but did not repair unrelated repository-wide inventory drift.

## How to Use Immediately

Open Codex CLI in this repository or in a project where ARIS Codex skills are installed, then run:

```text
/skills
# choose claim-lit-trace
Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed. Produce a minimal reading guide.
```

Direct invocation is also supported when the skill is visible:

```text
/claim-lit-trace "Trace only the references needed to check whether Kobayashi's Theorem 8.11 can be used at the Harish-Chandra module level. Use local papers first. Do not chase broad GGP background unless directly needed."
```

Expected run output:

```text
docs/claim-lit-traces/<slug>/
├── TARGET_QUESTION.md
├── TARGETED_REFERENCE_TRACE.md
├── MINIMAL_READING_GUIDE.md
├── UNRESOLVED_CHECKS.md
└── CLAIM_LIT_TRACE_REPORT.md
```

`REFERENCE_TRACE_GRAPH.md` may also be created when the graph is small and target-specific.

## User-Facing README

The end-user README is:

```text
skills/skills-codex/claim-lit-trace/README.md
```

It explains what the skill does, when to use it, when not to use it, Codex CLI invocation, ready-to-copy examples, representation-theory examples, output files, evidence labels, local paper preparation, common failure modes, and limitations.

## Limitations

- The skill does not prove a theorem or certify theorem applicability.
- The skill does not do broad related-work expansion by default.
- Metadata-only sources remain labeled `CONFIRMED_FROM_METADATA`; users should not treat them as text verification.
- PDF text extraction depends on local availability of PDFs and tools such as `pdftotext`.
- Network-backed sources may be skipped or recorded as unresolved when unavailable.

## Intentionally Not Implemented

- No new helper script was added.
- Existing `research-lit`, `arxiv`, `openalex`, `semantic-scholar`, and `citation-audit` skills were not substantially modified.
- No OpenAlex or Semantic Scholar referenced-work fetch extension was added.
- No proof-writing, proof-checking, paper-writing, Monap integration, or full Math-ARIS pipeline was added.
- Existing repository-wide skill inventory drift unrelated to this skill was not repaired.

## Next Steps

- Use the skill on a local representation-theory question with PDFs in `papers/` or `literature/`.
- If a run has many `CONFIRMED_FROM_METADATA` or `UNVERIFIED` labels, add the relevant PDFs or exact theorem text and rerun.
- Consider a future optional helper only if repeated runs show the same deterministic citation-extraction gap.
