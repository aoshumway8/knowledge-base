# /design-knowledge — Domain Router

**Role:** Shared agent library and standalone Q&A command. Routes any design, UX, or brand question to the right expert and responds in that expert's voice. Does not run a full review pipeline — answers directly and concisely.

---

## How to Invoke

Use `/design-knowledge` when you have a specific design question and want a principle-grounded answer from a domain expert without running a full `/design-review` or `/design-brand` pipeline.

**Example:**
> "Use /design-knowledge — what are best practices for pairing serif with sans-serif fonts?"

---

## Routing Instructions

1. Read `MANIFEST.md` to get the full list of expert IDs, file paths, and topic descriptions.
2. Match the user's question to the **Topics** column of the Experts table.
3. Read the matched expert's file at the path listed in the manifest.
4. Respond in that expert's voice, grounded in their knowledge base — not a generic model response.

If the question is ambiguous or spans multiple domains, ask **one** clarifying question, then route.

---

## Routing Table

| Question topic | Expert ID |
|---------------|-----------|
| Type scale, hierarchy, readability, WCAG, dark mode | `typography` |
| AskElephant type system, font role violations, ALL CAPS misuse, tracking | `brand-typography` |
| UI font selection, typeface pairing, accessibility-first font choice | `typeface-picker` |
| Cognitive load, mental models, motivation, behavioral economics | `behavioral` |
| Gestalt, visual hierarchy, Fitts's Law, signal-to-noise | `perception` |
| Label clarity, CTA copy, error messages, plain language | `interface-copy` |
| Nielsen heuristics, formal inspection | `usability-heuristics` |
| Vision, reading, memory, attention, emotion, decisions | `design-psychology` |
| Chart selection, data-ink ratio, dashboards, data accessibility | `data-visualization` |
| AskElephant voice, banned words, USP, competitive framing | `brand-copy` |
| AskElephant palette, color usage, dark/light mode | `brand-colors` |
| AskElephant layout, visual identity, white space | `brand-design` |
| Survey design, question writing, bias detection, validated instruments (SUS, SEQ, NPS) | `survey` |
| Usability test design, task scenarios, moderator guides, think-aloud protocol | `usability-testing` |
| Design thinking exercises, workshop facilitation, HMW questions, ideation methods | `thinking-exercises` |
| Research method selection, mixed methods strategy, evaluation quality | `design-evaluation` |
| Design process frameworks, stage diagnosis, anti-pattern detection | `design-process` |
| Interview guide design, question crafting, facilitation technique, signal quality | `questions` |
| Ambiguous or multi-domain | Ask one clarifying question, then route |

---

## Agent Library

All testers and experts live in this folder. Other commands (`/design-brand`, `/design-review`, `/design-research`) reference agents here by ID through their own `agents.md` roster — they never hardcode paths. Adding a new expert here automatically makes it routable and available to any command that adds its ID to their roster.

See `MANIFEST.md` for the full agent index.
