---
name: design-brand
description: Dual-mode brand skill — create on-brand content, review existing content for brand alignment, or both (create then immediately audit). Mode is determined in Phase 0. The same brand expertise powers all three modes.
---

# Design Brand

Dual-mode brand skill: **create** on-brand content, **review** existing content for brand alignment, or **both** — create then immediately audit. The same brand expertise powers all three modes. Mode is determined in Phase 0.

---

## Agent Dispatch Pattern

This skill never hardcodes agent file paths. For every phase that requires agents:

1. Read `agents.md` to get the roster of agent IDs for that phase.
2. Read `../design-knowledge/MANIFEST.md` to resolve each agent ID to its file path.
3. Read the agent files at those paths and proceed.

This means this SKILL.md never changes when agents are added, removed, or renamed — only `MANIFEST.md` and `agents.md` change.

---

## Target Detection

| Trigger phrase | Mode |
|---------------|------|
| "review", "is this on-brand", "does this sound like us", "brand audit", "check this", "audit this" | **REVIEW** |
| "write", "create", "draft", "help me write", "generate", "write this for me" | **CREATE** |
| "make this on-brand", "brand this up", "rewrite this as AskElephant", "apply our brand", "fix this copy" | **BOTH** |
| Ambiguous | Ask in Phase 0 |

---

## Artifact Types

| Type | Description |
|------|-------------|
| `app-copy` | UI copy in the web app — from source files under `apps/web/src/` |
| `marketing-site` | Live marketing site at `https://askelephant.ai` |
| `pasted-content` | Copy, draft, or content pasted directly by the user |
| `deck` | Pitch deck or presentation — user describes or pastes slide content |
| `email` | Sales outreach, follow-up, or transactional email |
| `social` | LinkedIn post, tweet, or other social content |
| `one-pager` | Sales leave-behind or product one-pager |

---

## Phase 0: Mode & Intent Detection

Before touching any files or running any commands, determine the following. Detect from context first — ask only what is genuinely ambiguous.

**Step 1 — Mode** (detect from user phrasing using Target Detection table above; ask if unclear):

> "Are you creating something, reviewing something, or both — create then immediately audit?"

**Step 2 — Artifact type** (detect from context; ask if unclear):

> "What are we working with? (e.g. LinkedIn post, pitch deck, website section, email, UI copy)"

**Step 3 — Audience** (detect from artifact type; ask if unclear):

> "Who is the primary audience — investor, prospect, existing customer, or internal team?"

Package as `BRAND_INTENT`:

```
BRAND_INTENT = {
  mode: "create" | "review" | "both",
  artifactType: "app-copy" | "marketing-site" | "pasted-content" | "deck" | "email" | "social" | "one-pager",
  audience: "investor" | "prospect" | "customer" | "internal",
  channel: string  // derived from artifactType + audience combination
}
```

**If the user skips or doesn't answer:** Note `"Intent: not provided"` in the report header and proceed with reasonable defaults. Never invent specifics — evaluate or create for the broadest likely audience.

---

## Phase 1: Setup

Check for `BRAND_DECISIONS.md` in the repo root — if found, read it and calibrate all findings against documented intentional brand decisions.

**Conflict resolution:**

| Situation | Action |
|-----------|--------|
| `BRAND_DECISIONS.md` documents the exact pattern as intentional | **Suppress the finding.** Do not surface it at any severity. |
| `BRAND_DECISIONS.md` documents a related principle but not this specific implementation | **Keep the finding** and add: "Note: BRAND_DECISIONS.md principle [X] applies — confirm this is intentional." Downgrade severity by one level. |
| `BRAND_DECISIONS.md` is silent on this area | **Keep the finding** at full severity. |

**Route by mode:**

- **CREATE** → skip to [Phase 4C](#phase-4c-create-production)
- **REVIEW** → proceed to [Phase 2R](#phase-2r-review-content-extraction)
- **BOTH** → run Phase 4C (produce the artifact), then route the produced artifact into Phase 2R (Path C) as `pasted-content` and run the full review. Deliver the artifact first, the brand review beneath it.

---

## Phase 2R: Review Content Extraction

Branch on artifact type determined in Phase 0:

### Path A — App UI copy

Get changed frontend files:

```bash
git diff origin/main...HEAD --name-only | grep -E '^apps/web/src/.*\.(tsx|ts|css)$' || true
```

If the diff is empty, fall back to the most recently modified files under `apps/web/src/routes/` and `apps/web/src/components/`.

For each changed file, extract as `REVIEW_CONTEXT`:
- All user-facing strings: headings, labels, button text, empty states, tooltips, error messages
- Tailwind classes indicating visual weight (text size, font weight, color)
- Apparent component purpose and screen context

**CSS/token-only diff:** If no `.tsx` or JSX-rendering `.ts` files are changed, treat as style-only. Skip `copy-tester` (Dorothy). Dispatch `brand-colors` and `typography` experts only (resolve their paths via `MANIFEST.md`). Note "Style-only review — no copy surface" in report header.

### Path B — Marketing site

Browse the live site:

```
URL: https://askelephant.ai
```

Extract `REVIEW_CONTEXT`:
- Page title, hero headline, and primary CTA copy
- All section headings, subheadings, and body copy
- Navigation labels, footer links, button and link labels
- Visual weight signals: what is largest, most prominent, most contrasted
- Page sections and their order
- Any social proof, pricing, or conversion elements

Note review target as "Marketing site — https://askelephant.ai (live)" in report header.

### Path C — Pasted or described content

Use the user-provided content directly as `REVIEW_CONTEXT`. Extract:
- All copy as written
- Any visual or layout descriptions the user included
- Channel and audience context from `BRAND_INTENT`

Note review target as "Pasted content — [artifact type]" in report header.

---

## Phase 3: Tester Dispatch (REVIEW mode only)

1. Read `agents.md` — get the tester IDs listed under **Testers (Phase 1)**.
2. Read `../design-knowledge/MANIFEST.md` — resolve each tester ID to its file path.
3. Read each tester file at the resolved path.
4. Spawn all testers **in parallel** using the Task tool. Each tester receives `REVIEW_CONTEXT`. Use `agent-context.md` to construct each prompt.

Each tester returns behavioral observations only — what they read, what registered, what felt off — plus a pass/fail verdict. No prescriptions. No brand principle names.

**Conditional dispatch:**

**Zoe (noise-tester) skip condition:** Skip Zoe when the artifact has no visual hierarchy signals — e.g., a pasted plain-text email draft with no subject line, formatting, or layout context. Note "(Zoe skipped — no visual surface)" in report header. Emit a `SkippedTester` record.

**Dorothy (copy-tester) skip condition:** Skip Dorothy when the artifact is purely visual with no copy — e.g., a CSS token change, a color palette spec, an icon set. Note "(Dorothy skipped — no copy surface)" in report header. Emit a `SkippedTester` record.

**Craig (cognitive-load-tester) skip condition:** Skip Craig when the artifact is a short-form piece with no information structure — e.g., a single headline, a tweet, a one-line CTA. Note "(Craig skipped — no multi-element structure to load-test)" in report header. Emit a `SkippedTester` record.

**Helen (brand-audience-tester):** Never skipped. Always dispatch.

**Remy (brand-fanatic-tester):** Never skipped. Always dispatch.

Return format: `TesterOutput` as defined in `../design-knowledge/types.md` (narrowed by local `types.md`).

Wait for all testers to return before proceeding to Phase 4.

---

## Phase 4: Expert Dispatch (REVIEW) / Phase 4C: Create Production

### REVIEW mode

1. Read `agents.md` — get the expert IDs listed under **Experts (Phase 2)**.
2. Read `../design-knowledge/MANIFEST.md` — resolve each expert ID to its file path.
3. Read each expert file at the resolved path.
4. Spawn all expert agents **in parallel** using the Task tool. Each expert receives `REVIEW_CONTEXT` and the complete Phase 3 tester output. Use `agent-context.md` to construct each prompt.

**Expert lane boundaries — apply these to prevent duplicated findings:**

| Topic | Primary Expert | Secondary (cross-lens context only) |
|-------|---------------|-------------------------------------|
| Voice notes, banned words, hype language | brand-copy | — |
| USP integrity, execution-first vs. insight-first | brand-copy | — |
| Competitor territory (Gong/Fathom/Reevo framing) | brand-copy | — |
| Channel voice mix calibration | brand-copy | brand-design |
| Palette compliance, hex values | brand-colors | — |
| Usage ratio violations, accent overuse | brand-colors | brand-design |
| Dark/light mode color violations | brand-colors | brand-typography |
| Font role violations (serif for body, wrong weight) | brand-typography | typography |
| AskElephant type system compliance (font role, weight, ALL CAPS) | brand-typography | — |
| Type hierarchy, scale, readability, WCAG contrast | typography | brand-typography |
| ALL CAPS misuse, tracking, label conventions | brand-typography | typography |
| Layout structure, column grid, section order | brand-design | typography |
| White space, breathing room, density | brand-design | brand-colors |
| CTA placement and visual priority | brand-design | brand-copy |
| Information architecture, section order | brand-design | — |

**Cross-lens escalation:** When 2 or more experts independently produce findings with the same `rootCause` slug, the synthesizer escalates that finding one severity level in the final report. A finding already at `"high"` stays at `"high"`.

Wait for all experts to return before proceeding to Phase 5.

### Phase 4C: Create Production

1. **Apply the Three-Layer Brand System** — the brand knowledge is baked into the expert agent files loaded via the manifest:

   - **Layer 1 — Strategic alignment**: Does this advance the execution-first USP? Is it doing, not informing? Does it avoid insight-first framing?
   - **Layer 2 — Voice check**: Does it hit the right voice mix for this channel? Calibrate using the `brand-copy` expert's channel voice knowledge.
   - **Layer 3 — Visual/format alignment**: Does layout, color, and type match the brand spec? Calibrate using the `brand-colors`, `brand-design`, and `typography` expert knowledge.

2. **Produce the artifact.** For longer outputs, apply the layer-check section by section, not just at the end.

3. **Run a self-review before delivering.** Scan for:
   - Copy failure modes: hype language, insight-first framing, passive voice hiding the value, competitor-sounding sentences, over-reliance on "workflow"
   - Visual failure modes: generic tech-blue, too many accent colors, serif in body, ALL CAPS used decoratively

   In pure CREATE mode: note any flags as brief inline comments or a "Watch for" callout at the end. Do not produce a full review report.

   In BOTH mode: route the produced artifact into Phase 2R as `pasted-content` and run the full review pipeline (Phases 2R → 3 → 4 → 5). Deliver the artifact first, the brand review beneath it.

---

## Phase 5: Final Report (REVIEW mode only)

Output the final report inline in the chat response. Do not write it to a file.

The report should feel like a sharp, brief Slack message from a brand-obsessed colleague — short, scannable, zero methodology. Marketers and engineers should be able to read the whole thing in under 2 minutes.

Tester observations appear as one-line evidence quotes in the tester's voice. Each quote must sound authentically like that persona:
- **Zoe** scanned it and is reporting what registered in 5 seconds — she sounds impatient and impressionistic
- **Remy** knows the brand cold and is reacting like someone with calibrated taste — she notices the instant something feels off-brand or generic
- **Dorothy** read every word carefully and is literally reporting what she understood (or didn't) — she sounds like a retired schoolteacher who is genuinely trying but will say plainly when something made no sense
- **Darryl** is tracking how much mental effort the content is demanding — she sounds like she already closed the tab if it made her think too hard
- **Helen** is a revenue professional who's been made to feel like a demographic afterthought by every AI tool she's tried — she reports whether the copy spoke to someone who has actually done the work, or just used the right nouns

Use this format exactly:

```
Brand Review — [name of what was reviewed]

─────────────────────────────────────────
🔴 Fix before you publish
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
| `"high"` | 🔴 Fix before you publish | Actively contradicts brand positioning or sends the wrong competitive message |
| `"medium"` | 🟡 Fix soon | Weakens brand clarity, misses the voice mark, or creates a notable visual inconsistency |
| `"low"` | 🟢 When you have time | Polish that separates good from unmistakably AskElephant |
| `"polish"` | 🟢 When you have time | One-word swap or one-class change — include only if genuinely trivial |

`"polish"` maps to the same 🟢 section as `"low"`. Omit polish findings if 🟢 already has 3 or more `"low"` findings — signal over noise.

**Deduplication:** Before writing the report, group all expert findings by their `rootCause` slug. When 2 or more experts share the same `rootCause`:
1. Merge into a single finding — keep the most specific `fix`, combine all `testerEvidence`, use the `primaryTester` from the finding with the most evidence.
2. Apply cross-lens escalation: raise severity one level (low → medium → high).
3. Discard the duplicates — only the merged finding appears.

When findings share similar but not identical `rootCause` slugs, use judgment: if they prescribe the same fix on the same element, merge them.

**Findings count caps:** Max 4 findings per severity section. Max 12 total. When cutting, prefer findings cited by multiple testers over single-tester findings.

---

## Context Window Management

Stay below ~35% context usage during agent spawning.

| What | This Agent Does | Specialist Agents Do |
|------|-----------------|----------------------|
| Artifact content | Reads/extracts as REVIEW_CONTEXT | Receive as extracted string |
| Tester reports | Collects and packages | Experts receive as Phase 3 dataset |
| Expert findings | Collects and synthesizes | Returned as structured JSON |
