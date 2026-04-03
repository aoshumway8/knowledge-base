# Design Knowledge Agent Manifest

Single source of truth for all agent IDs, file paths, and topic descriptions.
Commands never hardcode agent paths — they resolve IDs through this file.

**Adding an agent:** create the file + add one row. Nothing else changes.
**Removing an agent:** delete the file + remove the row (+ remove ID from any `agents.md` that listed it).
**Renaming or moving an agent:** update the path in one row only.

---

## Testers

| ID | File | Description |
|----|------|-------------|
| noise-tester | testers/noise-tester.md | Signal-to-noise, visual clutter, distraction |
| copy-tester | testers/copy-tester.md | Label clarity, CTA wording, plain language |
| cognitive-load-tester | testers/cognitive-load-tester.md | Working memory, decision fatigue, form complexity |
| mobile-tester | testers/mobile-tester.md | Touch targets, thumb zones, mobile-first patterns |
| vibes-tester | testers/vibes-tester.md | Emotional coherence, overall feel |
| empty-state-tester | testers/empty-state-tester.md | Zero-data, first-run, error recovery UX |
| brand-fanatic-tester | testers/brand-fanatic-tester.md | AskElephant voice, brand consistency |
| brand-audience-tester | testers/brand-audience-tester.md | Revenue professional audience comprehension, "is this for me?" test, jargon detection |

## Experts

| ID | File | Topics |
|----|------|--------|
| behavioral | experts/behavioral-expert.md | Motivation, behavioral economics, mental models |
| perception | experts/perception-expert.md | Gestalt, Fitts's Law, visual hierarchy, signal-to-noise |
| interface-copy | experts/interface-copy-expert.md | Label clarity, CTA copy, error messages |
| usability-heuristics | experts/usability-heuristics-expert.md | Nielsen heuristics, formal inspection |
| design-psychology | experts/design-psychology-expert.md | Vision, memory, attention, emotion |
| typography | experts/typography-expert.md | Type scale, hierarchy, readability, WCAG, dark mode |
| data-visualization | experts/data-visualization-expert.md | Chart selection, data-ink ratio, dashboards |
| brand-copy | experts/brand-copy-expert.md | AskElephant voice, banned words, USP, competitive framing |
| brand-colors | experts/brand-colors-expert.md | AskElephant palette, color usage, dark/light mode |
| brand-design | experts/brand-design-expert.md | AskElephant layout, visual identity, white space |
| brand-typography | experts/brand-typography-expert.md | AskElephant type system, font role violations, type hierarchy, ALL CAPS misuse, tracking |
| typeface-picker | experts/typeface-picker-expert.md | UI font selection, accessibility-first typeface pairing, Google Fonts |
| survey | experts/survey-expert.md | Survey design, question writing, measurement, response bias |
| usability-testing | experts/usability-testing-expert.md | Usability test design, task writing, moderated/unmoderated facilitation |
| thinking-exercises | experts/thinking-exercises-expert.md | Design thinking facilitation, ideation methods, problem framing |
| design-evaluation | experts/design-evaluation-expert.md | Evaluation method selection, research quality, when to use which method |
| design-process | experts/design-process-expert.md | Design process stages, iteration strategy, failure modes |
| questions | experts/questions-expert.md | Interview question design, anti-patterns, user research question crafting |
