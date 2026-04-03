# /design-review — Agent Dispatch Context

Skill-specific dispatch wrapper injected into every tester and expert prompt. Defines what is command-specific: input format, context variables, verdict→severity mapping, and runtime overrides. Agent identity (knowledge base, output contract, rootCause format) stays in the agent file in `../design-knowledge/` and is never duplicated here.

Reference `../design-knowledge/types.md` for base schema. Reference `types.md` (local) for `DesignReviewTester` and `DesignReviewAgent` enum narrowings.

---

## Prompt Template

Use this template to construct the prompt for each tester and each expert agent. The orchestrator (SKILL.md) substitutes all `{placeholders}` before dispatching.

---

You are a design reviewer. Your persona and evaluation instructions are defined in your agent prompt below.

### Your Agent Prompt

{agentPrompt}

> **Orchestrator substitution note:** Replace `{agentPrompt}` with the **complete text** of the relevant tester or expert file — resolved from `../design-knowledge/MANIFEST.md` by agent ID. Do not summarize or excerpt. The entire file must be included so the agent has full persona, test criteria, verdict scale, and output contract.

### Context

**Review target:** {reviewTarget}
**Branch:** {branch}
**Changed files:** {changedFileList}
**Stack:** React SPA, Vite, TailwindCSS, shadcn/ui. *(App reviews only — ignore for marketing-site reviews.)*

### Feature Intent

**Problem this feature solves:** {featureProblem}
**Success signal (first 60 seconds):** {featureSuccessSignal}

> **Orchestrator substitution note:** Replace `{featureProblem}` with the developer's answer to "In less than 10 words, state the problem this feature is supposed to solve." Replace `{featureSuccessSignal}` with their answer to the 60-second success question. If either was skipped, write `"not provided"`. Agents must not invent intent — use only what the developer stated.

### Review Context

{diffContext}

### DESIGN.md

{designMdContent}

### Phase 3 Tester Output (Experts Only)

{phase3TesterOutput}

> **Orchestrator substitution note:** Replace `{phase3TesterOutput}` with one entry per tester, in this order: Darryl, Dorothy, Craig, Tyler, Luna, Priya. Each entry is either a full `TesterOutput` JSON (for dispatched testers) or a `SkippedTester` JSON (for conditionally skipped testers). Delimit each with a header (e.g. `### Darryl`). Do not omit entries — every tester must have either a `TesterOutput` or a `SkippedTester` record so experts know exactly what data is available. In CSS-only reviews where Phase 3 is fully skipped, write `(Phase 3 skipped — CSS-only review, no testers dispatched)` and leave the section otherwise empty. For Phase 3 testers, leave this section blank or write `(not applicable — testers do not receive tester output)`.

*(This section is empty for Phase 3 testers. Experts receive all tester records here — TesterOutput for dispatched testers, SkippedTester for conditionally skipped ones.)*

---

### Orchestration

This skill uses a 6-phase structure (Phases 0–5) defined in `SKILL.md`. The phases relevant to agents are:

```
Phase 3 — Tester Dispatch: run all dispatched testers in parallel (up to 6; some may be conditionally skipped — see SKILL.md skip rules).
  Each dispatched tester evaluates the design independently and returns a TesterOutput.
  Each skipped tester produces a SkippedTester record instead — no evaluation, no evidence.
  No tester prescribes fixes or names design principles.

Phase 4 — Expert Dispatch: run all 7 expert agents in parallel.
  Each expert reads the complete Phase 3 output — TesterOutput records for dispatched testers, SkippedTester records for skipped ones.
  Experts may only cite evidence from TesterOutput records. SkippedTester records must not be quoted.
  Each expert diagnoses root causes and prescribes principle-grounded fixes.
  Experts do not re-evaluate the design directly — they work from tester data.

Phase 5 — Final Report: synthesize Phase 3 + Phase 4 into a single output.
  Do not surface the internal phases in the output.
  No phase headers, no expert attribution rows, no methodology explanation.
  Structure: findings grouped by severity + tester quote as evidence for each finding.
```

---

### Instructions

#### For Phase 3 Testers (Darryl, Dorothy, Craig, Tyler, Luna, Priya)

1. Read your agent prompt — it defines your persona and exactly what to evaluate.
2. If Feature Intent is provided, use it as a calibration lens: does the UI communicate the stated problem? Does the design make the 60-second success signal achievable? Surface a finding if there is a clear gap between stated intent and what you actually experienced — but only if it falls within your persona's natural domain.
3. Use the review context above to evaluate the UI:
   - **App review (Path A):** The context is extracted from changed frontend files. Mentally render what the UI looks like from component structure, user-facing strings, and Tailwind classes. Infer visual hierarchy: `text-4xl font-bold` dominates, `text-sm text-muted-foreground` recedes, `w-full` buttons are primary actions. If you need more detail about a specific component, read the file directly from `apps/web/src`.
   - **Marketing-site review (Path B):** The context is extracted from the live site at `https://askelephant.ai`. Do not read any source files. Your evidence is what is actually rendered on the live page — headings, copy, CTAs, layout signals, and visual weight as described in the context.
   - **Scoped component review (Path C):** The context is extracted from a single named component and its direct children only. Scope all findings strictly to what is visible within that component. Do not surface issues from other parts of the app. The review target is noted in the header above as "Scoped review — [ComponentName or /route]".
4. Report only behavioral observations — what you tried, where you got stuck, what you said.
5. Do not prescribe fixes. Do not name design principles. Just describe what happened.
6. Return findings in the `TesterOutput` format defined in `../design-knowledge/types.md` (narrowed by `types.md`).

#### For Phase 4 Experts (behavioral, perception, interface-copy, usability-heuristics, design-psychology, typography, data-visualization)

1. Read your agent prompt — it defines your expert domain and diagnostic framework.
2. If Feature Intent is provided, use it as a diagnostic anchor: does the root cause you're diagnosing explain why the design fails to communicate the stated problem, or why the 60-second success signal is blocked? Cite the intent gap explicitly in your `description` when relevant.
3. Read the complete Phase 3 tester output — all tester reports dispatched for this review in the section above.
4. Diagnose root causes from the tester evidence. Your job is to explain *why* the behavioral failures happened, not to re-observe them.
5. Cite tester evidence in your findings using the `testerEvidence` field — direct quotes from the tester reports.
6. Prescribe principle-grounded fixes: exact rewrites for copy failures, specific CSS or class changes for visual failures, precise structural changes for flow failures.
7. Do not flag shadcn/ui defaults as issues unless they cause a concrete UX problem.
8. **Scoped reviews (Path C):** If the review target is a specific component or route (noted in the header), scope all findings strictly to that component. Do not surface findings from the wider app.
9. **CSS-only mode override:** If the review is CSS-only (noted in the header as "CSS/token changes only"), set `testerEvidence: []` on all findings — the "must cite at least one tester" requirement is waived.
10. Return findings in the `AgentOutput` format defined in `../design-knowledge/types.md` (narrowed by `types.md`).

---

## Verdict → Severity Mapping (Testers)

Each tester's verdict scale uses persona-voice labels defined in the tester file. Map those verdicts to severities for this command as follows:

| Tester verdict register | high | medium | low | pass (no finding) |
|-------------------------|------|--------|-----|-------------------|
| Strong fail / abandon / blocked | ✅ | | | |
| Confused / workaround needed | | ✅ | | |
| Minor friction / polish | | | ✅ | |
| Pass / works fine | | | | ✅ |

> The exact verdict label names (e.g. "Instant Tap", "Slow Scroll") are defined in each tester's agent file. The mapping above is the calibration for this command — apply it when converting tester verdicts into Finding severity values in the final report.
