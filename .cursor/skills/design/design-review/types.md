# /design-review — Skill-Specific Type Narrowings

Base schema (`Finding`, `AgentOutput`, `TesterOutput`, `TesterFinding`, `SkippedTester`) lives in `../design-knowledge/types.md`. This file adds only the enum narrowings specific to this command.

---

## Tester Personas

Quick reference for all dispatched testers. Full persona, review process, and verdict scale live in each tester file — resolved from `../design-knowledge/MANIFEST.md` by ID.

| ID | Persona | Descriptor | Verdict scale |
|---|---|---|---|
| `noise-tester` | Darryl | 24, ADHD + dyslexia; bails in ~15 sec if the primary action isn't visually obvious | Instant Tap / Slow Scroll / Confused Pause / Quiet Exit |
| `copy-tester` | Dorothy | 72, retired schoolteacher; no smartphone, no tech vocabulary; trusts words over symbols | Gold Star / Needs a Grandson / Called the Telephone / Closed the Window |
| `cognitive-load-tester` | Craig | 47, Series C CEO; 4 monitors, 15 meetings/day; can't afford to read — can only recognize; tests extraneous cognitive load | Zero Tax / Acceptable Tax / Attention Debt / Synthesis Required / Context Lost / Fatal Interruption |
| `mobile-tester` | Tyler | Tests one-handed mobile usability; every interaction evaluated right-thumb-only | One-Thumb Pass / Stretch Required / Two-Hand Tax / Mis-Tap and Fumble / Gave Up |
| `vibes-tester` | Luna | Tests emotional coherence and overall feel; reports gut-first with sensory language | Full Send / Clean Enough / Flat / Choppy / Bad Vibes |
| `empty-state-tester` | Priya | Busy PM who skipped onboarding and wants to see if the product explains itself before committing to setup | Makes Sense / Needs a Nudge / Confused Blank / Brick Wall |

---

## Enum Narrowings

```typescript
type DesignReviewTester =
  | "darryl"
  | "dorothy"
  | "craig"
  | "tyler"
  | "luna"
  | "priya";

type DesignReviewAgent =
  | "behavioral"
  | "perception"
  | "interface-copy"
  | "usability-heuristics"
  | "design-psychology"
  | "typography"
  | "data-visualization";
```

---

## Severity Calibration

Verdict → severity mapping for this command. Apply when converting tester verdicts into `Finding.severity` values in the final report.

| Verdict | Tester | Severity |
|---|---|---|
| Quiet Exit | Darryl | high |
| Confused Pause | Darryl | medium |
| Slow Scroll | Darryl | low |
| Instant Tap | Darryl | pass |
| Closed the Window | Dorothy | high |
| Called the Telephone | Dorothy | medium |
| Needs a Grandson | Dorothy | low |
| Gold Star | Dorothy | pass |
| Fatal Interruption | Craig | high |
| Context Lost | Craig | high |
| Attention Debt | Craig | medium |
| Synthesis Required | Craig | medium |
| Acceptable Tax | Craig | low |
| Zero Tax | Craig | pass |
| Gave Up | Tyler | high |
| Mis-Tap and Fumble | Tyler | medium |
| Two-Hand Tax | Tyler | medium |
| Stretch Required | Tyler | low |
| One-Thumb Pass | Tyler | pass |
| Bad Vibes | Luna | high |
| Choppy | Luna | medium |
| Flat | Luna | low |
| Clean Enough | Luna | pass |
| Full Send | Luna | pass |
| Brick Wall | Priya | high |
| Confused Blank | Priya | medium |
| Needs a Nudge | Priya | low |
| Makes Sense | Priya | pass |

**CSS-only mode note:** When Phase 3 is skipped entirely (CSS/token changes only), no tester verdicts are produced. The `typography` expert sets `testerEvidence: []` on all findings — the verdict mapping above does not apply.
