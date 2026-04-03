# Design Knowledge — Shared Base Types

Canonical source for all shared interfaces. Each command's local `types.md` imports these definitions and adds only its own enum narrowings (tester names, agent names). Do not duplicate these interfaces in skill-local files.

---

```typescript
interface Finding {
  agent: string;              // narrowed per skill in local types.md
  severity: "high" | "medium" | "low" | "polish";
  rootCause: string;          // kebab-case, 3–6 words, neutral framing
  title: string;              // max 60 chars
  description: string;
  testerEvidence: string[];   // empty array valid in CSS-only / style-only reviews
  primaryTester?: string;     // narrowed per skill in local types.md
  element?: string;
  file?: string;
  fix: string;
  page: string;
}

interface AgentOutput {
  agent: string;
  findings: Finding[];
}

interface TesterFinding {
  verdict: string;            // named verdict from the tester's verdict scale
  observation: string;        // what the tester observed, in their voice — never prescriptive
  element?: string;
  page: string;
}

interface SkippedTester {
  tester: string;
  reason: string;
}

interface TesterOutput {
  tester: string;             // narrowed per skill in local types.md
  findings: TesterFinding[];
  skipped?: SkippedTester[];
}
```

---

## rootCause Slug Rules

- Format: kebab-case, 3–6 words
- Neutral framing — describes the failure, not the solution
- Examples: `label-text-too-vague`, `touch-target-below-minimum`, `primary-action-not-visible`, `type-contrast-insufficient`
- Used for cross-expert deduplication — consistent formatting is required
