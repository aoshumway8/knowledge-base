---
name: design-review
description: Run a multi-phase design review that mirrors professional UX practice. Phase 3 simulates usability testing: six user personas (Darryl, Dorothy, Craig, Tyler, Luna, Priya) each evaluate the design and report what broke, in plain behavioral terms. Phase 4 simulates the expert design review: seven specialist agents read the full usability data and apply design principles to diagnose root causes and prescribe polished fixes. The result is a report grounded in real-user evidence and guided by expert principles — the same process a designer follows, automated.
---

# Design Review

Multi-phase design review (Phases 0–5): gather feature intent, set up context, dispatch testers to observe behavior, dispatch experts to diagnose root causes, synthesize into a report. Testers and experts each run in parallel within their phase; output is a single Slack-style report — no methodology, just what's broken and how to fix it.

---

## Agent Dispatch Pattern

This skill never hardcodes agent file paths. For every phase that requires agents:

1. Read `agents.md` to get the roster of agent IDs for that phase.
2. Read `../design-knowledge/MANIFEST.md` to resolve each agent ID to its file path.
3. Read the agent files at those paths and proceed.

This means this SKILL.md never changes when agents are added, removed, or renamed — only `MANIFEST.md` and `agents.md` change.

---

## Stack Context

This repo's frontend is a React SPA (`apps/web`) using Vite, TailwindCSS, and shadcn/ui. Design tokens live in `apps/web/src/index.css`. The marketing site (`askelephant.ai`) is separate from the app UI (`app.askelephant.ai`).

All agents must calibrate findings to shadcn/ui conventions — do not flag patterns that are intentional shadcn/ui defaults unless they cause a concrete UX problem.

---

## Target Detection

| Trigger | Target |
|---------|--------|
| "app", "dashboard", "app.askelephant.ai", no qualifier | Web app UI (`http://localhost:3001`) — review is driven by `apps/web/src` PR diff |
| "marketing site", "askelephant.ai" | Marketing site (`https://askelephant.ai`) — Phase 2 skips diff-based context gathering; review the live site directly |
| Specific component name or route (e.g. "review the WorkflowEditor", "review /settings") | Scoped component review — Path C in Phase 2 |

**Default is the app.** The diff-based context gathering in Phase 2 reads `apps/web/src` exclusively, so unqualified reviews always target the app. To review the marketing site, the user must say "marketing site" or "askelephant.ai" explicitly — and in that case, Phase 2 should browse the live URL rather than reading PR diffs. For scoped reviews, use Path C.

---

## Phase 0: Feature Intent

Before touching any files or running any git commands, ask the questions below **one at a time** — wait for the developer's answer before asking the next.

**Step 1 — Ask this question and wait for an answer before continuing:**

> **"In less than 10 words, state the problem this feature is supposed to solve."**

**Step 2 — Only after the developer answers Step 1, ask this question and wait for an answer:**

> **"If you were watching a first-time user go through this in the first 60 seconds, what would they do that would tell you the design is working?"**

Once both answers are received, package them as `FEATURE_INTENT` and proceed to Phase 1. This flows to every tester and expert via the agent context template.

**If the developer skips or doesn't answer either question:** Note `"Feature intent: not provided"` in the report header and proceed. Agents must not invent intent from context — they evaluate what the UI communicates on its own.

**How agents use it:** Every tester and expert receives `FEATURE_INTENT` alongside `REVIEW_CONTEXT`. The problem statement anchors the "did the design communicate this?" lens. The success signal tells testers what first-60-second behavior the developer is hoping for — testers evaluate whether that behavior is actually achievable from the UI as shipped.

---

## Phase 1: Setup

Check for `DESIGN.md` in repo root — if found, read it and calibrate all findings against it.

**DESIGN.md conflict resolution:** When a finding contradicts a decision documented in `DESIGN.md`, apply this rule:

| Situation | Action |
|-----------|--------|
| DESIGN.md documents the exact pattern as intentional (e.g. "We intentionally use high-contrast borders on all cards") | **Suppress the finding.** Do not surface it at any severity. |
| DESIGN.md documents a related principle but not this specific implementation | **Keep the finding** and add a note: "Note: DESIGN.md principle [X] applies here — confirm this implementation is intentional." Downgrade severity by one level. |
| DESIGN.md is silent on this area | **Keep the finding** at full severity. |

The goal is to avoid surfacing findings that relitigate explicit product decisions, while still flagging implementations that may have drifted from intent.

Check working tree:
```bash
git status --porcelain
```
If dirty, note the uncommitted state in the report header so reviewers know the review was run against an unclean tree.

---

## Phase 2: Context Gathering

**Branch on target** determined in Phase 1 setup:

### Path A — App review (default)

Get the list of changed frontend files from the PR diff:

```bash
git diff origin/main...HEAD --name-only | grep -E '^apps/web/src/.*\.(tsx|ts|css)$' || true
```

If the diff is empty, note this in the report header ("No changed frontend files found — reviewing against current branch HEAD") and fall back to reading the most recently modified files under both `apps/web/src/routes/` and `apps/web/src/components/`. Prioritize route files first — they represent actual user flow surfaces — then supplement with component files that are referenced by those routes.

**CSS-only diff:** If the diff contains no `.tsx` files and no `.ts` files that render JSX (i.e., no files containing `return (`, `<Component`, or JSX-style markup), treat the review as CSS-only — there is no rendered surface for behavioral testers to evaluate. A `.ts` file that only exports token values, type definitions, or configuration constants does **not** disqualify the CSS-only path. When this condition is met, do not dispatch behavioral testers (Darryl, Dorothy, Craig, Tyler, Luna, Priya). Instead:
1. Note the review target as "CSS/token changes only — no JSX diff" in the report header.
2. Read all changed `.css` files as `REVIEW_CONTEXT`. Also read any changed `.ts` files that are non-JSX (token files, design system constants, theme config — i.e., files that do not contain `return (` or JSX markup) and include their full diff in `REVIEW_CONTEXT`. If the diff contains only `.ts` token files and no `.css` files, those `.ts` files are the sole `REVIEW_CONTEXT`.
3. Skip Phase 3 (Tester Dispatch) entirely.
4. Dispatch only the **Typography Expert** (`typography` ID) in Phase 4 — resolve its path from `../design-knowledge/MANIFEST.md`. Instruct the expert that no tester data was collected (CSS-only mode): they must evaluate the diff directly and set `testerEvidence: []` on all findings — the "must cite at least one tester" requirement is waived for this path.
5. The final report uses the typography expert's findings directly.

Read all changed component files. For each file extract and package as `REVIEW_CONTEXT`:
- The full JSX render output (what appears on screen)
- All user-facing strings: headings, labels, button text, placeholder text, error messages, empty states, tooltips, confirmation copy
- Tailwind classes that indicate visual weight (text size, font weight, color, layout)
- Conditional branches — what users see in each state (loading, empty, error, success)
- The component's apparent purpose (what screen or flow does this belong to?)

Skip: test files, storybook files, utility/hook files with no JSX output.

Also read `apps/web/src/index.css` and any relevant design token files to understand the base type scale and color system.

### Path B — Marketing site review

Do not read any source files or git diff. Instead, fetch the live site directly:

```
URL: https://askelephant.ai
```

Browse the page and extract `REVIEW_CONTEXT` from what is actually rendered:
- Page title, hero headline, and primary CTA copy
- All visible section headings and subheadings
- Navigation labels and footer links
- All button and link labels
- Any visible form fields, placeholder text, or error states
- Visual weight signals: what is visually largest, most prominent, or most contrasted
- Apparent page sections and their order
- Any visible social proof, pricing, or conversion elements

Note the review target in the report header as "Marketing site — https://askelephant.ai (live)" so findings are unambiguously scoped to the live site, not the app.

### Path C — Scoped component or route review

The user named a specific component or route (e.g. "WorkflowEditor", "the settings page", "/onboarding"). Do not read the PR diff or browse the live site.

1. **Locate the target.** Search `apps/web/src/` for the named component or route file:
   ```bash
   git diff origin/main...HEAD --name-only | grep -iE 'ComponentName|route-name' || true
   ```
   If not in the diff, find the file directly under `apps/web/src/routes/` or `apps/web/src/components/` by name.

2. **Read the target file in full.** This is the primary source of `REVIEW_CONTEXT`.

3. **Follow imports one level deep.** Read any child components directly imported and rendered by the target — but do not recurse further. This captures the full rendered surface without pulling in unrelated code.

4. **Extract `REVIEW_CONTEXT`** using the same fields as Path A (JSX output, user-facing strings, Tailwind classes, conditional states, component purpose).

5. **Scope findings strictly.** Testers and experts must only report issues observable within the named component or route. Do not surface findings from other parts of the app that happen to be in the import tree.

Note the review target in the report header as "Scoped review — [ComponentName or /route]" so findings are clearly scoped.

---

## Phase 3: Tester Dispatch

1. Read `agents.md` — get the tester IDs listed under **Testers (Phase 3)**.
2. Read `../design-knowledge/MANIFEST.md` — resolve each tester ID to its file path.
3. Read each tester file at the resolved path.
4. Spawn all testers in parallel using the Task tool. Each tester receives `REVIEW_CONTEXT` from Phase 2. Use the template in `agent-context.md` to construct each prompt.

Each tester returns behavioral observations only — what they tried, where they got stuck, what they said — plus a pass/fail verdict. No prescriptions. No design principle names.

**Tyler (mobile) conditional dispatch:** Tyler evaluates mobile usability. Skip Tyler and record an automatic pass when **all** of the following are true:
- The diff is Path A (app review) with no mobile-specific Tailwind classes (`sm:`, `md:`, `touch-`, `h-screen`, `w-screen`, `fixed bottom-`, etc.) in the changed files.
- The route or component is clearly desktop-only (e.g. admin panel, data table, settings form, analytics dashboard).
- There are no viewport-specific layout changes.

When Tyler is skipped, note it in the report header as "(Tyler skipped — desktop-only surface)" and emit a `SkippedTester` record: `{ tester: "tyler", skipped: true, reason: "desktop-only surface — no mobile classes in diff" }`.

**Priya (empty state) conditional dispatch:** Priya evaluates zero-data states and setup-skipped flows. Skip Priya and record an automatic pass when **all** of the following are true:
- The diff contains no empty state conditional renders: no components named `EmptyState`, `ZeroState`, `NoResults`, `Placeholder`, or similar; no `items.length === 0`, `!data`, or equivalent branches that render a distinct UI; no "no items yet" copy or placeholder illustrations.
- The route or component is clearly data-populated by design (e.g. a detail view, an edit form, a modal with required fields).

When Priya is skipped, note it in the report header as "(Priya skipped — no empty states in diff)" and emit a `SkippedTester` record: `{ tester: "priya", skipped: true, reason: "no empty states in diff — data-populated surface" }`.

Return format: `TesterOutput` as defined in `../design-knowledge/types.md` (narrowed by local `types.md`).

Wait for all testers to return before proceeding.

---

## Phase 4: Expert Dispatch

1. Read `agents.md` — get the expert IDs listed under **Experts (Phase 4)**.
2. Read `../design-knowledge/MANIFEST.md` — resolve each expert ID to its file path.
3. Read each expert file at the resolved path.
4. Spawn all experts in parallel using the Task tool. Each expert receives `REVIEW_CONTEXT` from Phase 2 **plus the complete Phase 3 tester output** (all tester reports).

Experts diagnose root causes and prescribe principle-grounded fixes. They do not re-evaluate the design directly — they work from tester data. Each expert reads all tester reports, not just the ones most obviously in their domain.

**Expert lane boundaries** — the Behavioral Expert and Design Psychology Expert have overlapping knowledge bases. To prevent systematic duplication, apply these lanes:

| Topic | Primary Expert | Secondary (cross-lens only) |
|-------|---------------|------------------------------|
| Cognitive load, mental models, affordances, gulf of execution/evaluation | behavioral | design-psychology |
| Motivation, SDT, flow state, habit formation, goal-gradient | behavioral | — |
| Behavioral economics: anchoring, loss aversion, endowment, IKEA effect | behavioral | — |
| Vision: peripheral, pattern recognition, Gestalt, Fitts's Law | perception | design-psychology |
| Memory: working memory limits, primacy/recency, chunking, recognition vs recall | design-psychology | perception |
| Attention: selective, sustained, filtering, multitasking | design-psychology | behavioral |
| Emotion, mood, trust, social proof | design-psychology | behavioral |
| Decision-making, choice overload, unconscious decisions | design-psychology | behavioral |
| Copy, labels, error messages, CTA text | interface-copy | — |
| Nielsen/Shneiderman/ISO heuristics, formal inspection | usability-heuristics | — |
| Type scale, hierarchy, readability, WCAG, dark mode | typography | — |
| Chart selection, data-ink ratio, lie factor, encoding hierarchy | data-visualization | perception |
| 'So what' layer: benchmarks, trend indicators, insight-driven titles, annotations | data-visualization | — |
| Dashboard progressive disclosure, tier structure, 3-30-300 time gates | data-visualization | behavioral |
| Color in data visualization: categorical/sequential/diverging scales, colorblind safety | data-visualization | perception |
| Data accessibility: SVG, alt text, data table toggle, Chartability framework | data-visualization | — |
| Mobile data visualization: chart curation, responsive tables, touch targets | data-visualization | — |

When a topic falls in your lane, diagnose it fully. When it falls in another expert's primary lane, mention it at most as supporting context for your own finding — do not produce a standalone finding for it.

**Cross-lens escalation rule:** If 2 or more experts diagnose the same root cause (matching `rootCause` slug) from different frameworks, the synthesizer escalates that finding one severity level in the final report.

Wait for all experts to return before proceeding.

---

## Phase 5: Final Report

Output the final report inline in the chat response. Do not write it to a file.

The report must feel like a quick Slack message from a brutally honest colleague — short, scannable, a little funny. Engineers should be able to read the whole thing in under 2 minutes.

**Do not surface the two phases in the output.** No phase headers, no expert attribution rows, no methodology explanation. The internal process is invisible. The output is just: here's what's broken, here's the evidence, here's the fix.

Tester observations appear as one-line evidence quotes — short, dry, and in character. Each quote must sound authentically like that persona. Dorothy sounds like an actual 72-year-old. Craig sounds mildly annoyed. Darryl sounds like she already closed the tab. Tyler sounds like she's doing this one-handed while walking. Luna sounds like a calm, gut-first vibes check with sensory language ("clean", "choppy", "good vibes", "bad vibes"). Priya sounds like a busy PM who skipped setup and is now mildly annoyed the product won't explain itself ("I just wanted to see if it worked before doing all that").

Use this format exactly:

```
Design Review — [name of what was reviewed]

─────────────────────────────────────────
🔴 Fix before you ship
─────────────────────────────────────────

1. [Short, plain-English problem title]
   "[Direct quote or observation from the tester who caught it]" — [Tester name]
   → [Concrete fix. One or two sentences max.]

─────────────────────────────────────────
🟡 Fix soon
─────────────────────────────────────────

2. ...

─────────────────────────────────────────
🟢 When you have time
─────────────────────────────────────────

3. ...
```

**Severity definitions:**

| Finding severity | Report section | Meaning |
|-----------------|----------------|---------|
| `"high"` | 🔴 Fix before you ship | Blocks completion or causes abandon |
| `"medium"` | 🟡 Fix soon | Causes confusion or requires a workaround |
| `"low"` | 🟢 When you have time | Polish and minor friction |
| `"polish"` | 🟢 When you have time | Separates better from great — include only if trivial to fix |

`"polish"` findings appear in the same 🟢 section as `"low"`, but only include them if the fix is genuinely trivial (a single word change, one class swap). Omit polish findings entirely if the 🟢 section already has 3 or more `"low"` findings — signal over noise.

**Deduplication:** Before writing the report, group all expert findings by their `rootCause` slug. When 2 or more experts share the same `rootCause`:
1. Merge into a single finding — keep the most specific `fix`, combine `testerEvidence` from all versions, and use the `primaryTester` from the finding with the most evidence quotes.
2. Apply cross-lens escalation: raise the severity by one level (low → medium → high). A finding already at `"high"` stays at `"high"`.
3. Discard the duplicate findings — only the merged version appears in the report.

When findings share similar but not identical `rootCause` slugs (e.g. `"no-cta-weight"` and `"cta-buried-in-noise"`), use judgment — if they prescribe the same fix on the same element, merge them.

**Findings count caps:** Cap each severity section at **4 findings maximum**. If experts return more, keep the highest-severity and best-evidenced ones. Total report findings must not exceed **12**. When cutting, prefer findings cited by multiple testers over single-tester findings.

---

## Context Window Management

Stay below **~35% context usage** during agent spawning.

| What | This Agent Does | Specialist Agents Do |
|------|-----------------|----------------------|
| Source files / live site | Reads once for REVIEW_CONTEXT | Receive as extracted string |
| Tester reports | Collects and packages | Experts receive as Phase 1 dataset |
| Expert findings | Collects and synthesizes | Returned as structured JSON |
