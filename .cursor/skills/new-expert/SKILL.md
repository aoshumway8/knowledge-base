# New Expert

Create a new expert agent and place it in the correct location.

---

## File Split

| File | Contains | Changes when |
|------|----------|--------------|
| **Expert file** (`design-knowledge/experts/`) | Identity + domain knowledge + output contract | Domain knowledge needs updating |
| **`agent-context.md`** (each command) | Input format description, operating procedure addenda, runtime overrides, schema enum binding | The command's pipeline changes |

Path: `design-knowledge/experts/lowercase_underscores_expert.md`

> Before writing: **What does this expert know that the model doesn't?** Specific standards, failure pattern catalogs, mechanical detection tests. If the answer is "nothing proprietary," the expert adds no consistent value.

---

## Expert File Sections

**1. Header** — Agent ID (kebab-case) + primary lane: one sentence naming what this expert owns and what adjacent domain it will not touch as a primary finding.

**2. POV** *(optional)* — A specific evaluative lens, not a biography. Earns its place only if it changes how the expert weighs evidence.

**3. Core Knowledge Base** — The highest-leverage section. Must include:
- Named frameworks/standards with specific citable rules — not "low contrast" but "WCAG SC 1.4.3: 4.5:1 for normal text; #777777 on white = 4.47:1 — fails, cannot be rounded up"
- Detection logic that is mechanical (a test), not judgmental — "scan for font-weight < 600 adjacent to body text," not "check if hierarchy feels clear"
- Failure pattern catalog: slug-tagged failure modes with surface signatures
- Canonical correct examples — what passing looks like (most skipped, most valuable)

**4. Scope Boundaries** — In scope, out of scope (name the adjacent expert), overlap handling table.

**5. Domain Checklist** — Ordered domain-specific tests only. No operating procedure steps ("read testers first" belongs in `agent-context.md`). Lead with highest-impact checks.

**6. Severity Mapping** — Domain-specific thresholds for `"high"` / `"medium"` / `"low"` / `"polish"`.

**7. Output Contract** — Identity expressed as output format. Travels with the expert file, not with any command. Include:
- rootCause slug format: 3–6 words, kebab-case, no expert name embedded. 3–5 domain examples.
- Required field semantics: `title` (max 60 chars, name the failure not the principle), `description` (principle → mechanism → failure), `fix` (exact rewrite or specific class — never "improve X")
- testerEvidence default: "must cite at least one tester" (runtime overrides injected by the command take precedence)
- Ethics flag: when/how to add an ethics note in `description` — mandatory for manipulation-risk domains; state N/A if not applicable
- Feedback style: one sentence on this expert's diagnostic voice

---

## Quality Checklist

**Expert file:**
- [ ] Knowledge base injects proprietary context (not reformatted training data)
- [ ] Detection logic is mechanical (a test) not judgmental (a call)
- [ ] Canonical correct examples present alongside failure patterns
- [ ] Scope boundaries: in scope, out of scope, overlap handling, adjacent experts named
- [ ] Domain checklist: ordered, domain-specific, no operating procedure steps
- [ ] Severity mapping: concrete domain-specific thresholds
- [ ] Output contract: rootCause examples, field semantics, testerEvidence default, ethics flag rule, feedback style
- [ ] No operating procedure, AgentOutput struct binding, or task block in this file

**`agent-context.md` (when deploying this expert):**
- [ ] Standalone dispatch: lead-with-diagnosis instruction present
- [ ] Pipeline dispatch: tester-data-first step explicit; input format described
- [ ] Runtime overrides explicit (e.g., CSS-only testerEvidence waiver)
- [ ] Schema enum binding references this command's `types.md` — field semantics stay in the expert file

---

## Output Template

```
Expert: [name / id]
Path: design-knowledge/experts/[lowercase_underscores_expert.md]
Primary lane: [one sentence — what it owns and explicitly does not]

- [ ] Knowledge base injects proprietary context
- [ ] Detection logic is mechanical
- [ ] Canonical correct examples present
- [ ] Scope boundaries: in scope, out of scope, overlap handling, adjacent experts named
- [ ] Domain checklist: ordered, domain-specific, no operating procedure steps
- [ ] Severity mapping: domain-specific thresholds
- [ ] Output contract: rootCause examples, field semantics, testerEvidence default, ethics flag, feedback style
- [ ] No operating procedure, AgentOutput struct binding, or task block in this file
```
