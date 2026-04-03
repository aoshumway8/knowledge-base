# /design-research — Skill-Specific Type Narrowings

Base schema (`Finding`, `AgentOutput`, `TesterOutput`, `TesterFinding`, `SkippedTester`) lives in `../design-knowledge/types.md`. This file adds only the enum narrowings specific to this command.

---

## Enum Narrowings

```typescript
type DesignResearchAgent =
  | "usability-testing"
  | "survey"
  | "thinking-exercises"
  | "design-evaluation"
  | "design-process"
  | "questions";

type DesignResearchMode =
  | "plan"
  | "build"
  | "review"
  | "facilitate"
  | "diagnose";

type DesignResearchArtifact =
  | "interview-guide"
  | "usability-test"
  | "survey"
  | "exercise"
  | "evaluation-plan"
  | "process-audit"
  | "other";

type DesignStage =
  | "generative"
  | "formative"
  | "summative"
  | "unclear";
```

## Notes

- There are no testers in this skill. `TesterOutput` and `TesterFinding` from the base types are not used.
- `AgentOutput` is used only in **REVIEW** mode. For all other modes, experts deliver output in plain markdown — no JSON wrapper.
- `Finding.testerEvidence` is always `[]` in this skill — there are no testers to cite. The field is kept for base type compatibility.
- `Finding.primaryTester` is always `undefined` in this skill.
