# /design-brand — Skill-Specific Type Narrowings

Base schema (`Finding`, `AgentOutput`, `TesterOutput`, `TesterFinding`, `SkippedTester`) lives in `../design-knowledge/types.md`. This file adds only the enum narrowings specific to this command.

---

## TODO: Port tester persona table from `/askelephant-brand` types.md

Port the tester persona table (Zoe, Remy, Helen) with profiles and verdict scales.

---

## Enum Narrowings

```typescript
type BrandTester = "zoe" | "remy" | "helen" | "dorothy" | "craig";

type BrandAgent =
  | "brand-copy"
  | "brand-colors"
  | "brand-design"
  | "typography"
  | "brand-typography";
```

---

## TODO: Port severity calibration table from `/askelephant-brand` types.md

Port the severity calibration table with brand-specific thresholds (high / medium / low / polish).

---

## TODO: Port Common rootCause Slugs reference list from `/askelephant-brand` types.md
