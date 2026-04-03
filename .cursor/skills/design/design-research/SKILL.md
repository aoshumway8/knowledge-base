# /design-research — Design Research Workflows

Route any design research question — planning, building, facilitating, or reviewing — to the right expert and deliver a directly usable output. No methodology explanation. No phase headers in the output. Just the deliverable the user asked for.

---

## Agent Dispatch Pattern

This skill never hardcodes agent file paths. For every phase that requires agents:

1. Read `agents.md` to get the roster of agent IDs.
2. Read `../design-knowledge/MANIFEST.md` to resolve each agent ID to its file path.
3. Read the agent files at those paths and proceed.

---

## Phase 0: Intent Detection

Before touching any files, determine what the user needs. Detect from their phrasing first — ask only what is genuinely ambiguous.

**Detect the mode:**

| Trigger phrase | Mode |
|---|---|
| "what research should I do", "what method", "should I run interviews or surveys", "how do I evaluate" | **PLAN** |
| "write", "draft", "help me write", "create", "build" | **BUILD** |
| "review", "audit", "check this", "is this good" | **REVIEW** |
| "run", "facilitate", "walk me through", "workshop", "exercise" | **FACILITATE** |
| "our process", "we keep building the wrong thing", "we skip research", "broken" | **DIAGNOSE** |
| Ambiguous | Ask one clarifying question, then route |

**Detect the artifact type** (detect from context; ask only if unclear):

| Artifact | Expert(s) |
|---|---|
| Interview guide, research questions, discussion guide | `questions` |
| Usability test plan, task scenarios, moderator guide | `usability-testing` |
| Survey, questionnaire, validated instrument (SUS, SEQ, NPS, UEQ) | `survey` |
| Design thinking exercise, workshop, facilitation sequence | `thinking-exercises` |
| Research method selection, mixed methods strategy, evaluation plan | `design-evaluation` |
| Design process audit, stage diagnosis, process framework selection | `design-process` |
| Multi-domain or ambiguous | See routing logic below |

Package as `RESEARCH_INTENT`:

```
RESEARCH_INTENT = {
  mode: "plan" | "build" | "review" | "facilitate" | "diagnose",
  artifact: "interview-guide" | "usability-test" | "survey" | "exercise" | "evaluation-plan" | "process-audit" | "other",
  stage: "generative" | "formative" | "summative" | "unclear",   // design stage if known
  experts: string[]   // resolved in Phase 1
}
```

---

## Phase 1: Expert Routing

Route to the minimum set of experts that covers the user's question. Never dispatch more experts than the question requires.

### Single-expert routing

| User need | Expert |
|---|---|
| Write or review an interview guide | `questions` |
| Write or review a usability test plan, task scenarios, or moderator guide | `usability-testing` |
| Write, audit, or interpret a survey; select a validated instrument | `survey` |
| Facilitate or sequence a design thinking exercise | `thinking-exercises` |
| Select a research method; design a mixed methods study; audit research quality | `design-evaluation` |
| Diagnose a broken design process; select a process framework | `design-process` |

### Multi-expert routing

Dispatch multiple experts **in parallel** only when the question genuinely spans domains:

| Question | Experts |
|---|---|
| "What research should I do and how should I structure it?" | `design-evaluation` + `design-process` |
| "I need to plan research and write the guide" | `design-evaluation` → then `questions` (sequential: plan first, guide second) |
| "Review my entire research plan" (method + guide) | `design-evaluation` + `questions` |
| "We're building the wrong thing — what should we do?" | `design-process` + `design-evaluation` |
| "Help me plan and run a usability test from scratch" | `design-evaluation` + `usability-testing` |

### Context questions before dispatch

Ask **one** clarifying question if any of these are unknown and would change which expert is dispatched:

- What stage of the design process are you in? (generative / formative / summative)
- What decision will this research inform?
- Do you have existing materials to review, or are you starting from scratch?
- What constraints do you have? (time, budget, participant access)

Do not ask all four. Pick the one question whose answer would most change the routing.

---

## Phase 2: Context Gathering

Collect whatever context is needed to give the expert enough information to produce a useful output.

**For PLAN and DIAGNOSE modes:**
- Ask the user to describe the product, feature, or team situation in 2–3 sentences if they haven't already.
- Ask what decision this research will inform.
- Note any constraints: timeline, budget, participant access.

**For BUILD mode:**
- Ask for the research objective and the decision it will inform if not provided.
- Ask whether they are starting from scratch or have existing materials to build on.

**For REVIEW mode:**
- Ask the user to paste or describe the artifact to be reviewed.
- Note the research objective the artifact is designed to serve.

**For FACILITATE mode:**
- Ask what problem or stage they are in (Empathize / Define / Ideate / Prototype / Test).
- Ask how many participants and how much time they have.

Package everything as `RESEARCH_CONTEXT` and pass it to each expert.

---

## Phase 3: Expert Dispatch

1. Read `agents.md` — get the expert IDs for this routing.
2. Read `../design-knowledge/MANIFEST.md` — resolve each expert ID to its file path.
3. Read each expert file at the resolved path.
4. If multiple experts are needed in parallel, spawn them simultaneously using the Task tool. Each expert receives `RESEARCH_CONTEXT` and `RESEARCH_INTENT`. Use the template in `agent-context.md` to construct each prompt.
5. If experts must run sequentially (e.g., plan before guide), run the first expert, incorporate its output into context, then dispatch the next.

**Expert lane boundaries** — when multiple experts are dispatched together, apply these lanes to prevent duplicated output:

| Topic | Primary | Secondary |
|---|---|---|
| Research method selection and evaluation strategy | `design-evaluation` | `design-process` |
| Design process stage diagnosis and framework selection | `design-process` | `design-evaluation` |
| Interview question crafting, facilitation, signal quality | `questions` | `design-evaluation` |
| Usability test design, task scenarios, moderated/unmoderated | `usability-testing` | `design-evaluation` |
| Survey design, bias auditing, validated instruments | `survey` | `design-evaluation` |
| Design thinking exercises and workshop facilitation | `thinking-exercises` | `design-process` |

When a topic falls in your primary lane, cover it fully. When it falls in another expert's primary lane, mention it at most as brief supporting context — do not produce standalone output for it.

Wait for all experts to return before proceeding to Phase 4.

---

## Phase 4: Deliver Output

Output the final deliverable inline in the chat response. Do not write it to a file.

**Output format by mode:**

### PLAN
A clear, actionable research plan. Include:
- Recommended method(s) with rationale
- Why this method fits the current design stage and decision
- What it will and won't answer
- Suggested sample size and session format
- What to do with the output (how findings feed the design decision)

No methodology preamble. Start with the recommendation.

### BUILD
The actual artifact — fully drafted, ready to use:
- **Interview guide**: grand tour question, 5–8 major questions with probes, probe bank, session flow with timing
- **Usability test plan**: test objective, method recommendation, 3–7 task scenarios with success criteria, metrics plan, moderator guide
- **Survey**: every question drafted with type rationale, scale specs, bias audit, length + completion time estimate
- **Exercise facilitation guide**: exercise name, time, participant count, step-by-step instructions, what a completed output looks like

No preamble. Deliver the artifact directly.

### REVIEW
A tight, scannable findings list. Use this format:

```
Research Review — [artifact type]

─────────────────────────────────────────
🔴 Fix before you use this
─────────────────────────────────────────

1. [Short problem title]
   → [Concrete fix. One or two sentences.]

─────────────────────────────────────────
🟡 Fix soon
─────────────────────────────────────────

2. ...

─────────────────────────────────────────
🟢 When you have time
─────────────────────────────────────────

3. ...
```

Severity definitions:

| Section | Meaning |
|---|---|
| 🔴 Fix before you use this | Will produce invalid, misleading, or unusable data |
| 🟡 Fix soon | Will degrade data quality or signal reliability |
| 🟢 When you have time | Polish that improves precision or participant experience |

Cap at 4 findings per section, 10 total. Prefer findings that affect data validity over cosmetic ones.

### FACILITATE
Step-by-step facilitation guide:
- Exercise name, time, participant count
- Setup and materials needed
- Step-by-step instructions
- What a completed output looks like (so the team knows when they're done)
- What to do with the output next (feeds which design stage)

### DIAGNOSE
A direct process audit:
- Name the specific anti-pattern or stage failure
- Explain the downstream harm
- Prescribe the corrective action at the stage level
- Name the exercise or method that fixes it

---

## Expert Lane Boundaries in Output

When multiple experts contribute to a single output, synthesize before delivering. Do not deliver two separate reports. Merge findings by topic, resolve overlaps using the lane boundaries above, and deliver one coherent output.

**Deduplication for REVIEW mode:** Group findings by `rootCause` slug. If two experts diagnose the same root cause, merge into one finding, keep the most specific fix, and raise severity by one level (low → medium → high). Max `"high"` stays `"high"`.

---

## Context Window Management

Stay below ~35% context usage during expert dispatch.

| What | This Agent Does | Expert Agents Do |
|---|---|---|
| User's research materials | Reads/extracts as RESEARCH_CONTEXT | Receive as extracted string |
| Expert outputs | Collects and synthesizes | Returned as structured output |
