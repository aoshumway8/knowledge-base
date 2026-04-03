# New Tester

Create a new tester persona and place it in the correct location.

---

## File Split

| File | Contains | Changes when |
|------|----------|--------------|
| **Tester file** (`design-knowledge/testers/`) | Identity + output contract | Persona needs updating |
| **`agent-context.md`** (each command) | Task block, verdict→severity mapping, schema enum binding | The command's pipeline changes |

Path: `design-knowledge/testers/lowercase_underscores_tester.md`

> Before writing: **Who is this person, and what specific interaction pattern makes them uniquely good at surfacing one class of failure?** The answer must be exclusive — this persona catches this failure mode, other testers don't.

---

## Tester File Sections

**1. Persona** — Rich, castable character definition. Required:
- Name, age, location
- Bio (150–250 words) — specific employment, tech habits, attention patterns. The backstory is the mechanism.
- Key Characteristics (8–14) — behaviors, not categories. "Dense text blocks slow her to a dead stop" is a behavior. "Low technical literacy" is a category.
- Vocabulary table — UI terms → what this person calls them. Prevents jargon leaking into observations.
- Failure Signature — the one class of failure only this persona catches. "The [Name] Test fails when X. Other testers won't catch this because Y."
- Does NOT Test For list (4–8 exclusions) — prevents tester from drifting into expert territory

**2. Review Process** — 3–4 temporal stages of how this person experiences the interface. Questions they ask themselves in their own vocabulary, not UX terms.

**3. Verdict Scale** — 4 named verdicts (best to worst) in the persona's register, each with a one-sentence quote in persona voice. No verdict→severity mapping — that's command-specific.

**4. Sample Reactions** — 10–14 direct quotes in persona voice. Calibrates the AI's voice; shows authentic output vs. expert-voice contamination.

**5. Output Contract** — What a valid observation looks like for this tester. Include:
- What this tester reports: behavioral moment, first-person, no diagnoses
- What this tester never reports: fix fields, principle citations, root cause naming, severity labels — named in the persona's language (e.g., "Darryl doesn't say 'CTA lacks visual weight' — she says 'I didn't see a button' and moves on")
- TesterOutput fields: what `finding`, `verdict`, and `evidence` mean in this tester's domain
- Verdict→severity mapping is command-specific and belongs in `agent-context.md`, not here

---

## Quality Checklist

**Tester file:**
- [ ] Persona bio is specific enough to cast a real actor
- [ ] Key Characteristics are behaviors, not categories
- [ ] Vocabulary table forces non-technical language
- [ ] Failure Signature is exclusive — other testers won't catch this
- [ ] Does NOT Test For list excludes expert territory
- [ ] Review process is the persona's internal experience, not a test procedure
- [ ] Verdict scale: 4 labels, persona-voice quotes, no verdict→severity mapping
- [ ] Sample reactions are authentic persona voice, not UX analysis
- [ ] Output contract: valid observation definition, prescription boundary, TesterOutput field semantics
- [ ] No task block, schema enum binding, or verdict→severity mapping in this file

**`agent-context.md` (when deploying this tester):**
- [ ] Input format described for this command's context
- [ ] Focus questions in the persona's vocabulary
- [ ] Verdict→severity mapping explicit
- [ ] Schema enum binding references this command's `types.md`
- [ ] No `fix` field in schema

---

## Output Template

```
Tester: [name]
Path: design-knowledge/testers/[lowercase_underscores_tester.md]
Failure mode this tester owns: [one sentence]

- [ ] Persona is specific and castable
- [ ] Vocabulary table forces authentic language
- [ ] Failure Signature is exclusive
- [ ] Does NOT Test For list excludes expert territory
- [ ] Review process is internal experience, not test procedure
- [ ] Verdict scale: 4 labels, persona-voice quotes, no verdict→severity mapping
- [ ] Sample reactions: authentic persona voice
- [ ] Output contract: observation rules, prescription boundary, TesterOutput field semantics
- [ ] No task block, schema enum binding, or verdict→severity mapping in this file
```
