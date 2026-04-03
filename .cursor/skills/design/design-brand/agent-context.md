# /design-brand — Agent Dispatch Context

Skill-specific dispatch wrapper injected into every tester and expert prompt. Defines what is command-specific: input format, context variables, verdict→severity mapping, and runtime overrides. Agent identity (knowledge base, output contract, rootCause format) stays in the agent file in `../design-knowledge/` and is never duplicated here.

Reference `../design-knowledge/types.md` for base schema. Reference `types.md` (local) for `BrandTester` and `BrandAgent` enum narrowings.

---

## Prompt Template

Use this template to construct the prompt for each tester and each expert agent. The orchestrator (SKILL.md) substitutes all `{placeholders}` before dispatching.

---

You are a brand reviewer. Your persona and evaluation instructions are defined in your agent prompt below.

### Your Agent Prompt

{agentPrompt}

> **Orchestrator substitution note:** Replace `{agentPrompt}` with the **complete text** of the relevant tester or expert file — resolved from `../design-knowledge/MANIFEST.md` by agent ID. Do not summarize or excerpt. The entire file must be included so the agent has full persona, evaluation criteria, verdict scale, and output contract.

---

### Context

**Review target:** {reviewTarget}
**Artifact type:** {artifactType}
**Primary audience:** {audience}
**Channel:** {channel}
**Mode:** {mode}

> **Orchestrator substitution note:** `{reviewTarget}` is the plain-English name of what is being reviewed (e.g. "LinkedIn post — Q2 product launch", "hero copy — askelephant.ai", "pitch deck — Series A"). `{artifactType}` and `{audience}` come from `BRAND_INTENT`. `{channel}` is derived from artifactType + audience.

---

### Brand Intent

**Artifact description:** {artifactDescription}
**Stated goal:** {statedGoal}

> **Orchestrator substitution note:** Replace `{artifactDescription}` with the user's description of what this artifact is and what it needs to do. Replace `{statedGoal}` with any stated intent the user provided in Phase 0 ("this is for a Series B pitch", "this is going out to our warm prospect list next week"). If either was skipped, write `"not provided"`. Agents must not invent intent — use only what was stated.

---

### Review Context

{reviewContext}

> **Orchestrator substitution note:** Replace `{reviewContext}` with the extracted artifact content from Phase 2R:
> - **Path A (app copy):** Extracted user-facing strings, Tailwind classes, and component context from changed `.tsx` files
> - **Path B (marketing site):** Extracted page content from the live site at `https://askelephant.ai`
> - **Path C (pasted content):** The user's content verbatim, plus any layout or visual context they described
>
> Include everything visible to an audience member — all copy, all visual weight signals, all structure. Do not truncate.

---

### Phase 3 Tester Output (Experts Only)

{phase3TesterOutput}

> **Orchestrator substitution note:** Replace with one entry per tester, in this order: Zoe, Remy, Helen, Dorothy, Craig. Each entry is either a full `TesterOutput` JSON (for dispatched testers) or a `SkippedTester` JSON (for conditionally skipped testers). Delimit each with a header (e.g. `### Zoe`). Do not omit entries — every tester must have either a `TesterOutput` or a `SkippedTester` record so experts know exactly what data is available.
>
> In style-only reviews where Phase 3 copy testers are fully skipped, write `(Phase 3 partially skipped — style-only review, copy testers not dispatched)` and include only the Remy `TesterOutput` (Remy is never skipped).
>
> For Phase 3 testers, leave this section blank or write `(not applicable — testers do not receive tester output)`.

*(This section is empty for Phase 3 testers. Experts receive all tester records here.)*

---

### Orchestration

This skill uses a phased structure defined in `SKILL.md`. The phases relevant to agents are:

```
Phase 3 — Tester Dispatch: run all dispatched testers in parallel (up to 5; some
  may be conditionally skipped — see SKILL.md skip conditions).
  Each dispatched tester evaluates the artifact from their audience persona.
  Each tester returns impressions — what they read, what registered, what felt off.
  Skipped testers produce a SkippedTester record — no evaluation, no evidence.
  No tester prescribes fixes or names brand principles.

Phase 4 — Expert Dispatch: run all 5 expert agents in parallel.
  Each expert reads the complete Phase 3 output — TesterOutput records for dispatched
  testers, SkippedTester records for skipped ones.
  Experts may only cite evidence from TesterOutput records.
  SkippedTester records must not be quoted as evidence.
  Each expert diagnoses root causes and prescribes brand-standard-grounded fixes.
  Experts do not re-evaluate the artifact directly — they work from tester data.

Phase 5 — Final Report: synthesize Phase 3 + Phase 4 into a single output.
  No phase headers, expert attribution rows, or methodology explanation.
  Structure: findings grouped by severity + tester quote as evidence for each finding.
```

---

### Instructions

#### For Phase 3 Testers (Zoe, Remy, Helen, Dorothy, Craig)

1. Read your agent prompt — it defines your persona and exactly what to evaluate.
2. Use the review context to experience the artifact as your persona would in real life. Do not analyze it clinically — react to it.
3. Report only impressions and observations — what you read, what registered first, what the brand felt like, what you understood or didn't.
4. Do not prescribe fixes. Do not name brand principles or reference the brand guide. Just describe what you experienced.
5. Return findings in the `TesterOutput` format defined in `../design-knowledge/types.md` (narrowed by `types.md`).

#### For Phase 4 Experts (brand-copy, brand-colors, brand-design, typography, brand-typography)

1. Read your agent prompt — it defines your expert domain and diagnostic framework.
2. Read the complete Phase 3 tester output above.
3. Diagnose root causes from the tester evidence. Your job is to explain *why* the brand failures happened, not to re-observe them.
4. Cite tester evidence in your findings using the `testerEvidence` field — direct quotes from tester reports only. No fabricated quotes.
5. Prescribe specific, actionable fixes: exact rewrites for copy failures, hex values for color failures, Tailwind classes or CSS for visual failures.
6. Stay within your expert lane boundaries as defined in `SKILL.md`.
7. **Style-only mode override:** If the review is style-only (noted in the header as "Style-only review — no copy surface"), set `testerEvidence: []` on all findings — the "must cite at least one tester" requirement is waived.
8. Return findings in the `AgentOutput` format defined in `../design-knowledge/types.md` (narrowed by `types.md`).

---

## Verdict → Severity Mapping (Testers)

Each tester's verdict scale uses persona-voice labels defined in the tester file. Map those verdicts to severities for this command as follows:

| Tester verdict register | high | medium | low | pass (no finding) |
|-------------------------|------|--------|-----|-------------------|
| Strong fail / off-brand / wrong message lands | ✅ | | | |
| Confused / unclear / felt generic | | ✅ | | |
| Minor off-note / polish opportunity | | | ✅ | |
| Pass / on-brand / clear | | | | ✅ |

> The exact verdict label names are defined in each tester's agent file. The mapping above is the calibration for this command — apply it when converting tester verdicts into Finding severity values in the final report.
