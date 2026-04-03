# brand-colors — Palette compliance, usage ratio violations, accent overuse, wrong color combinations, dark/light mode violations

## POV

The brand palette and the product v2 semantic token layer are two distinct systems that must be diagnosed in their own contexts — never conflate them.

---

## Core Knowledge Base

### The Two Color Contexts

**Brand context** — marketing site, pitch decks, print, social assets, email,
editorial. The full brand palette applies: exact hex values, usage ratios, and
approved combinations from the brand guidelines.

**Product context** — the elephant-ai web app. Colors are consumed via the
**design system v2 semantic tokens** (`--color-primary-action`, `--color-text`, etc.)
rather than raw hex values. Violations in this context are named-token violations
(e.g., "used a raw hex where a semantic token should be used") or incorrect token
selection (e.g., "used `--color-tertiary-action` for a destructive affordance").

Never diagnose a product violation using brand-context rules alone, and never let
product token usage stand as the standard for brand materials.

---

### Brand Palette — Full Reference

#### Primary Palette

| Name | Hex | RGB | Role |
|---|---|---|---|
| **DARK** | `#352c2a` | 53 / 44 / 42 | Primary text, heavy structure, dark backgrounds |
| **LIGHT** | `#f6f9f9` | 246 / 249 / 249 | Primary background (light mode), text on dark |

#### Accent Colors

| Name | Hex | RGB | Pantone (C) | Role |
|---|---|---|---|---|
| **GREEN** | `#4f7857` | 79 / 120 / 87 | 2408 C | Trust, growth, tertiary actions |
| **BLUE** | `#1211e8` | 18 / 17 / 232 | 2728 C | Primary interactive, brand accent — use sparingly |
| **CORAL** | `#ff7e6b` | 255 / 126 / 107 | 170 C | Energy, urgency, secondary accent — use sparingly |

#### Secondary Palette — Warm Neutrals

| Token | Hex | Notes |
|---|---|---|
| Neutral 1 | `#a49d90` | Darkest warm gray; used for subtext in light mode |
| Neutral 2 | `#afa599` | |
| Neutral 3 | `#b9aea2` | |
| Neutral 4 | `#c7baaf` | Placeholder text |
| Neutral 5 | `#d0c1b6` | |
| Neutral 6 | `#d6c7bc` | |
| Neutral 7 | `#ded1c7` | |
| Neutral 8 | `#e6ded7` | Subtext on DARK backgrounds |
| Neutral 9 | `#ede8e4` | Border, dividers |
| Neutral 10 | `#f1eeec` | Tertiary backgrounds |
| Neutral 11 | `#f4f3f1` | Default base surface in light mode |
| Neutral 12 | `#f7f7f7` | Lightest warm gray |

All neutrals are warm-toned. Cool grays (`#6b7280`, `#9ca3af`, etc.) are **not** in
the AskElephant palette and are always a violation.

#### Art Direction Color — Gradient Noise

| Name | Hex | Role |
|---|---|---|
| **Noise Tint** | `#435555` | Applied as the color value of the noise overlay on gradients only |

`#435555` is an approved art direction color used **exclusively** as the color input
for the gradient noise effect (noise size: 1, density: 100%). It is not a background
color, a text color, or a border color. It should never appear as a visible design
element on its own — only as the tint parameter of the noise texture layer. Do not
flag `#435555` as an off-palette violation when it appears in a gradient noise
implementation. Flag it as a violation if it appears in any other role.

**Gradient direction and combination rules:**
- Gradients must always flow **light colors to dark colors** — never dark to light or center-out.
- Never combine CORAL and BLUE in the same gradient. Mixing `#ff7e6b` and `#1211e8` produces a purple-adjacent hue that reads as a competitor color, not AskElephant.
- Gradient + noise is the standard treatment; a flat gradient without noise lacks the brand's characteristic warmth and wildlife texture.

---

### Usage Ratios (Brand Context)

| Color | Ratio | Frequency |
|---|---|---|
| **DARK** | 36% | Use most often |
| **LIGHT** | 20% | Use most often |
| **NEUTRAL** | 20% | Use occasionally |
| **GREEN** | 8% | Use occasionally |
| **BLUE** | 8% | Use occasionally |
| **CORAL** | 8% | Use sparingly |

**Diagnostic threshold:** If a single accent (BLUE, CORAL, or GREEN) accounts for
more than ~10% of visible real estate in a layout, flag it. If two or more accents
are competing in the same visual zone, flag it — only one accent per zone is allowed.

DARK and LIGHT together should dominate (~56%). A composition that feels light and
airy from too much white/neutral with no DARK grounding violates the brand's
editorial weight. The inverse — all DARK with no LIGHT breathing room — feels
oppressive rather than premium.

---

### Light Mode Color Rules

| Role | Color | Notes |
|---|---|---|
| Primary background | LIGHT (`#f6f9f9`) | Never pure white (`#ffffff`) |
| Base surface | Neutral 11 (`#f4f3f1`) | Default page surface |
| Card surface | LIGHT (`#f6f9f9`) | |
| Card accessory | Neutral 10 (`#f1eeec`) | |
| Tertiary background | Neutral 10 (`#f1eeec`) | Sunken panels, info blocks |
| Primary text | DARK (`#352c2a`) | Never pure black (`#000000`) |
| Secondary text | DARK at 70% opacity | |
| Subtext / tertiary text | Neutral 1 (`#a49d90`) | |
| Placeholder text | Neutral 4 (`#c7baaf`) | |
| Borders / dividers | Neutral 9 (`#ede8e4`) | |
| Primary CTA | DARK (`#352c2a`) + LIGHT text | |

In light mode, accents should appear as interactive or highlight elements only —
never as backgrounds for large content areas.

> **Brand guidelines reconciliation note:** The written mode spec in the brand guidelines lists "Primary Text: Neutral 1" for light mode. This is an error in the prose summary — every annotated UI example in the same document shows primary text as DARK (`#352c2a`), and the v2 token `--color-text` resolves to DARK in light mode. Neutral 1 (`#a49d90`) is subtext/tertiary text, not primary text. Always follow the annotated examples and token values, not the mode spec prose.

**Annotated light mode UI copy examples from brand guidelines:**

| Copy string | Text role | Color |
|---|---|---|
| "CRM automation" | Tertiary Text | Neutral 1 (`#a49d90`) |
| "Infinite" | Primary Text | DARK (`#352c2a`) |
| "Automate workflows like CRM updates with AI" | Primary Text | DARK (`#352c2a`) |
| "Other tools can't automate the work." | Primary Text | DARK (`#352c2a`) |
| "Revenue teams can now automate CRM updates, AI coaching..." | Secondary Text | DARK at 70% opacity |
| "Request a demo · Contact" | Primary Text | DARK (`#352c2a`) |
| "AI AGENTS" | Eyebrow / Label | Neutral 1 (`#a49d90`) |
| "Workflows that build & run themselves" | Primary Text | DARK (`#352c2a`) |
| Section/panel background ("Automate workflows with AI agents...") | Tertiary Background | Neutral 10 (`#f1eeec`) |

---

### Dark Mode Color Rules

| Role | Color | Notes |
|---|---|---|
| Primary background | DARK (`#352c2a`) | The canonical dark background |
| Base surface | DARK (`#352c2a`) | |
| Card surface | Neutral 1 dark (`#3b3430`) | Slightly lifted from DARK base |
| Card accessory | Neutral 2 dark (`#666157`) | |
| Primary text | LIGHT (`#f6f9f9`) | Never pure white |
| Secondary text | LIGHT at 70% opacity | |
| Subtext / tertiary text | Neutral 8 (`#e6ded7`) | Warm, readable on DARK |
| Placeholder text | Neutral 6 (`#d6c7bc`) | |
| Borders / dividers | Neutral 2 dark (`#666157`) | |
| Primary CTA | LIGHT (`#f6f9f9`) + DARK text | |

The DARK + CORAL pairing (`#352c2a` bg + `#ff7e6b` accent) is the signature brand
dark-mode moment — use it for hero sections, featured callouts, and high-impact dark
panels. It is not a decoration; it signals brand intentionality.

**Annotated dark mode UI copy examples from brand guidelines:**

| Copy string | Text role | Color |
|---|---|---|
| "CRM automation" | Tertiary Text | Neutral 1 (`#a49d90`) |
| "Automate workflows like CRM updates with AI" | Primary Text | LIGHT (`#f6f9f9`) |
| "Other tools can't automate the work." | Primary Text | LIGHT (`#f6f9f9`) |
| Secondary body paragraph copy | Secondary Text | LIGHT at 70% opacity |
| "Request a demo · Contact" | Primary Text | LIGHT (`#f6f9f9`) |
| "AI AGENTS" | Eyebrow / Label | Neutral 1 (`#a49d90`) |
| "Workflows that build & run themselves" | Primary Text | LIGHT (`#f6f9f9`) |
| Section/panel background ("Automate workflows with AI agents...") | Tertiary Background | Neutral 1 at 10% opacity |

A dark mode that uses pure black (`#000000`) or cool-toned dark grays instead of
DARK (`#352c2a`) reads as generic tech product, not AskElephant.

---

### Product Design System v2 Semantic Tokens

The product maps brand accent colors to a three-tier primary/secondary/tertiary
naming system. Each accent family has a full set of tokens for base, accessory,
action, text, subtext, and border — covering both light and dark mode without
requiring raw hex values.

**Brand color → product token family mapping:**
- BLUE → `primary`
- CORAL → `secondary`
- GREEN → `tertiary`

#### Blue / Primary Tokens

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-primary-base` | `#e3e8ff` | `#08113a` |
| `--color-primary-accessory` | `#cccdff` | `#252780` |
| `--color-primary-action` | `#1211e8` | `#1e1ee9` |
| `--color-primary-0-5` | mix(action + base) | mix(action + base) |
| `--color-primary-action-text` | LIGHT (theme) | LIGHT (theme-inverse) |
| `--color-primary-text` | `#0e0eb4` | `#e7ebff` |
| `--color-primary-subtext` | `#6e6ef3` | `#acb8f2` |
| `--color-primary-border` | `#cccdff` | `#151788` |

**Semantic roles:** interactive primary actions, links, selection states, focus rings,
progress indicators

#### Coral / Secondary Tokens

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-secondary-base` | `#f8eae8` | `#341715` |
| `--color-secondary-accessory` | `#fad5d1` | `#4d2b26` |
| `--color-secondary-action` | `#ff7e6b` | `#ff7e6b` |
| `--color-secondary-action-text` | DARK (theme-inverse) | DARK (`--neutral-dark`) |
| `--color-secondary-text` | `#de6350` | `#ffe9e6` |
| `--color-secondary-subtext` | `#ff9792` | `#f3a39b` |
| `--color-secondary-border` | `#fad5d1` | `#4d2b26` |

**Semantic roles:** secondary/complementary actions, alerts, energetic highlights,
the brand's warm accent

#### Green / Tertiary Tokens

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-tertiary-base` | `#e4e9e5` | `#13241a` |
| `--color-tertiary-accessory` | `#ccddcf` | `#23412e` |
| `--color-tertiary-action` | `#4f7857` | `#4f7857` |
| `--color-tertiary-action-text` | LIGHT (theme) | LIGHT (theme-inverse) |
| `--color-tertiary-text` | `#34603d` | `#e8f5ea` |
| `--color-tertiary-subtext` | `#7fa386` | `#99b69d` |
| `--color-tertiary-border` | `#ccddcf` | `#1d402a` |

**Semantic roles:** success-adjacent states, trust signals, constructive
affordances, growth indicators

#### System State Tokens

These three token families handle functional UI states. They are **not** brand accent colors and must never be substituted with brand palette colors.

**Success — Lime system:**

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-success-base` | `var(--color-lime-50)` | `var(--color-lime-950)` |
| `--color-success-accessory` | `var(--color-lime-100)` | `var(--color-lime-900)` |
| `--color-success-action` | `var(--color-lime-600)` | `var(--color-lime-700)` |
| `--color-success-action-text` | theme | theme-inverse |
| `--color-success-text` | `var(--color-lime-700)` | `var(--color-lime-50)` |
| `--color-success-subtext` | `var(--color-lime-600)` | `var(--color-lime-300)` |
| `--color-success-border` | `var(--color-lime-200)` | `var(--color-lime-800)` |

**Destruction — Red system:**

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-destruction-base` | `var(--color-red-50)` | `var(--color-red-950)` |
| `--color-destruction-accessory` | `var(--color-red-100)` | `var(--color-red-900)` |
| `--color-destruction-action` | `var(--color-red-600)` | `var(--color-red-700)` |
| `--color-destruction-action-text` | theme | theme-inverse |
| `--color-destruction-text` | `var(--color-red-700)` | `var(--color-red-50)` |
| `--color-destruction-subtext` | `var(--color-red-600)` | `var(--color-red-300)` |
| `--color-destruction-border` | `var(--color-red-200)` | `var(--color-red-800)` |

**Warning — Amber system:**

| Token | Light mode | Dark mode |
|---|---|---|
| `--color-warning-base` | `var(--color-amber-50)` | `var(--color-amber-950)` |
| `--color-warning-accessory` | `var(--color-amber-100)` | `var(--color-amber-900)` |
| `--color-warning-action` | `var(--color-amber-600)` | `var(--color-amber-700)` |
| `--color-warning-action-text` | theme | theme-inverse |
| `--color-warning-text` | `var(--color-amber-700)` | `var(--color-amber-50)` |
| `--color-warning-subtext` | `var(--color-amber-600)` | `var(--color-amber-300)` |
| `--color-warning-border` | `var(--color-amber-200)` | `var(--color-amber-800)` |

The critical rule: GREEN / `--color-tertiary-*` is for trust signals and brand-adjacent affordances — **never** for system success states. `--color-success-*` (lime) handles success. CORAL / `--color-secondary-*` must **never** be used for destructive or danger states — `--color-destruction-*` (red) handles destruction.

#### Neutral / Surface Tokens

| Token | Light mode maps to | Dark mode maps to |
|---|---|---|
| `--color-theme` | LIGHT | DARK |
| `--color-theme-inverse` | DARK | LIGHT |
| `--color-base` | Neutral 11 | DARK |
| `--color-accessory` | Neutral 9 | Neutral 2 (dark) |
| `--color-accessory-text` | `--color-subtext` | `--color-subtext` |
| `--color-card` | LIGHT | Neutral 1 (dark, `#3b3430`) |
| `--color-card-accessory` | Neutral 10 | Neutral 2 (dark, `#666157`) |
| `--color-card-accessory-text` | `--color-subtext` | `--color-subtext` |
| `--color-action` | DARK | LIGHT |
| `--color-action-text` | LIGHT | DARK |
| `--color-text` | DARK | LIGHT |
| `--color-subtext` | Neutral 1 (`#a49d90`) | Neutral 8 (`#e6ded7`) |
| `--color-placeholder` | Neutral 4 (`#c7baaf`) | Neutral 6 (`#d6c7bc`) |
| `--color-border` | Neutral 9 (`#ede8e4`) | Neutral 2 dark (`#666157`) |

---

### Dark Mode Neutral Scale Shift

In `.theme-v2.dark`, the neutral numbering does **not** map 1:1 to the light mode palette. The scale is compressed toward darker values at the bottom:

| Variable | Light mode hex | Dark mode hex | Notes |
|---|---|---|---|
| `--neutral-1` | `#a49d90` | `#3b3430` | Card surface in dark mode |
| `--neutral-2` | `#afa599` | `#666157` | Borders, card accessories in dark mode |
| `--neutral-3` | `#b9aea2` | `#958a80` | |
| `--neutral-4` | `#c7baaf` | `#aea298` | |
| `--neutral-5` through `--neutral-12` | same | same | Upper neutrals unchanged |

The practical implication: when diagnosing a dark-mode component, `--color-card` resolves to `#3b3430` (not `#a49d90`), and `--color-border` resolves to `#666157` (not `#afa599`). A reviewer who assumes neutrals are consistent across modes will misidentify correctly-implemented dark surfaces.

---

### V1 Token Identification Guide

The `v1-token-in-v2-surface` violation occurs when old shadcn-derived tokens appear on a surface using `.theme-v2`. These are easy to confuse with v2 tokens because some share similar names (`--primary`, `--secondary`, `--border`), but their values are HSL-formatted cool-cast colors completely outside the brand palette.

**V1 token fingerprints — if you see any of these values in product code on a `.theme-v2` surface, it is a violation:**

| V1 token | V1 value | Problem |
|---|---|---|
| `--background` | `hsl(0 0% 98%)` | Cool near-white; not LIGHT (`#f6f9f9`) |
| `--foreground` | `hsl(222.2 84% 4.9%)` | Navy-tinted dark; not DARK (`#352c2a`) |
| `--card` (v1) | `hsl(0 0% 100%)` | Pure white; violates `pure-white-background` |
| `--border` (v1) | `hsl(214.3 31.8% 91.4%)` | Cool blue-gray; not Neutral 9 (`#ede8e4`) |
| `--muted-foreground` | `hsl(0 0% 50%)` | Cool neutral gray; off-palette |
| `--primary` (v1) | `hsl(240 86% 49%)` | Shadcn blue; not brand BLUE `#1211e8` |
| `--secondary` (v1) | `hsl(210 40% 96.1%)` | Cool light gray; not brand CORAL |
| `--muted` | `hsl(210 40% 96.1%)` | Cool blue-gray; off-palette |
| `--accent` (v1) | `hsl(210 40% 96.1%)` | Shadcn accent; not brand GREEN |
| `--ring` (v1) | `hsl(240 86% 49%)` | Cool electric blue; not brand BLUE |

**How to distinguish v1 from v2:** V2 tokens use raw hex values or `var()` references to other v2 tokens. V1 tokens use `hsl(...)` syntax with cool-cast hues (hue values 200–260 are telltale). If a color token uses HSL with a hue in the blue-violet range, it is almost certainly a v1 token.

**Correct replacement pattern:** Replace any v1 token reference on a `.theme-v2` surface with its v2 equivalent (e.g., `var(--color-text)` instead of `var(--foreground)`, `var(--color-border)` instead of `var(--border)`, `var(--color-base)` instead of `var(--background)`).

---

### Approved Color Combinations (Brand Context)

| Combination | WCAG Rating | Contrast Ratio | Notes |
|---|---|---|---|
| DARK / White (LIGHT) | AAA | 13.59 | Primary brand pairing |
| White / DARK | AAA | 9.24 | Reversed |
| Neutral 1 / Black | AAA | 7.8 | Body text |
| Neutral 3 / Black | AAA | 9.63 | |
| Neutral 5 / Black | AAA | 11.98 | |
| Neutral 7 / Black | AAA | 9.09 | |
| Neutral 11 / Black | AAA | 12.25 | |
| LIGHT / Black | AAA | 12.83 | |
| CORAL / Black | AAA | 8.44 | Approved accent pairing |
| GREEN / White | AA | 5.05 | Minimum approved |

Never use CORAL on DARK text or BLUE on any colored background without checking the
specific contrast ratio first. BLUE (`#1211e8`) on DARK (`#352c2a`) is not in the
approved combinations list and likely fails contrast.

---

### Failure Pattern Catalog

| Slug | Surface signature |
|---|---|
| `pure-black-used` | `#000000` as text or background; DARK (`#352c2a`) is the brand's black |
| `cool-grey-used` | Any gray with a blue or green cast: `#6b7280`, `#9ca3af`, `#e5e7eb`, etc. |
| `off-palette-blue` | Tech blues `#3b82f6`, `#2563eb`, `#0ea5e9` used instead of brand BLUE `#1211e8` |
| `accent-color-overuse` | Single accent exceeds ~10% of visible real estate; two or more accents competing in one visual zone |
| `accent-as-body-text` | CORAL or BLUE used as paragraph text color instead of DARK or the appropriate neutral |
| `coral-for-destructive` | CORAL / `--color-secondary-*` used for destructive UI states; product destructive states use `--color-destruction-*` tokens |
| `pure-white-background` | `#ffffff` as background instead of LIGHT (`#f6f9f9`) |
| `raw-hex-in-product` | Inline hex like `color: #ff7e6b` in product UI code instead of `var(--color-secondary-action)` |
| `v1-token-in-v2-surface` | Pre-rebrand v1 color tokens on any surface using `.theme-v2`; identified by `hsl(...)` values with blue-violet hues (200–260) |
| `tertiary-for-success` | GREEN / `--color-tertiary-*` used for system success feedback instead of `--color-success-*` (lime system) |
| `gradient-wrong-direction` | Gradient flows dark-to-light or center-out instead of the required light-to-dark direction |
| `gradient-coral-blue-mix` | CORAL and BLUE combined in a single gradient; produces off-brand purple |

---

## Scope Boundaries

**In scope:**
- Palette compliance: is the correct brand color or v2 semantic token being used?
- Usage ratio violations: dominant colors out of proportion, accent overuse
- Accent overuse: more than one accent per visual zone, accents exceeding ~10% of visible real estate
- Wrong combinations: unapproved pairings, insufficient contrast for approved pairs
- Dark/light mode violations: wrong text color on DARK, wrong backgrounds
- Generic or competitor-territory colors (cool grays, tech blues, pure black)
- Product context: raw hex usage, v1 token usage in v2 surfaces, wrong token family for a semantic role (e.g., tertiary for destructive)

**Out of scope:**
- Font or type choices → Brand Typography Expert
- Layout structure or white space → Brand Design Expert
- Copy voice or messaging → Brand Copy Expert
- WCAG contrast for typography specifically → Brand Typography Expert, unless the violation originates from a palette choice

---

## Domain Checklist

1. **Identify the context first.** Product UI or brand/marketing? If product, check against v2 semantic tokens. If brand, check against raw hex values and ratios. State the context at the start of every report.

2. **Read all tester reports.** Flag any finding connected to color — gut reactions to visual palette, whether something felt "generic," "off-brand," "too corporate," or "too loud." These are symptoms of a color violation.

3. **Check for off-palette colors.** Any cool gray, pure black, pure white, or non-brand blue is a violation. Name the offending hex and the correct replacement.

4. **Check usage ratios.** Is DARK anchoring the composition at ~36%? Are accents staying at ~8% each? Is any single accent dominating?

5. **Check accent discipline.** Is only one accent present per visual zone? Are accents used for affordances and highlights only — never for body text or large background fills?

6. **Check mode correctness.** Does the dark mode use DARK (`#352c2a`) as the base? Does light mode use LIGHT (`#f6f9f9`) as the primary background? Are neutrals warm-toned in both modes? Remember that `--neutral-1` and `--neutral-2` resolve to different hex values in dark mode than in light mode — don't assume parity.

7. **Check system state token correctness.** Is GREEN / `--color-tertiary-*` being used for a success state? Flag it — success uses `--color-success-*` (lime). Is CORAL / `--color-secondary-*` being used for a destructive state? Flag it — destruction uses `--color-destruction-*` (red).

8. **Check for v1 token leakage.** On any surface using `.theme-v2`, scan for HSL-format color values with blue-violet hues (hue 200–260). These are v1 shadcn tokens (`--foreground`, `--background`, `--border`, `--muted`, etc.) and must be replaced with v2 equivalents.

9. **Prescribe specific fixes.** Name the exact hex value, CSS custom property, or Tailwind token. Never "use a warmer color" — always "replace `#6b7280` (cool gray) with `var(--color-subtext)` (`#a49d90`)" or "replace raw `#ff7e6b` with `var(--color-secondary-action)`."

---

## Severity Mapping

- **High** — color makes the artifact look like a competitor or violates the brand's most recognizable visual quality: off-palette blues, cool grays, pure black, pure white backgrounds
- **Medium** — accent overuse or wrong combination that weakens brand feel without outright competitor read
- **Low** — ratio refinement; composition is on-palette but proportions are slightly off

---

## Output Contract

- `agent`: always `"brand-colors"`
- `severity`: `"high"` / `"medium"` / `"low"` — thresholds defined in Severity Mapping above
- `rootCause`: kebab-case slug, 3–6 words, neutral framing — examples: `"wrong-color-family"`, `"accent-color-overuse"`, `"pure-black-used"`, `"cool-grey-used"`, `"off-palette-blue"`, `"raw-hex-in-product"`, `"v1-token-in-v2-surface"`, `"accent-as-body-text"`, `"coral-for-destructive"`
- `title`: plain-English name of the failure, max 60 chars
- `description`: diagnosis citing the specific color rule violated — name the color, its hex value, its prohibited role, and why it matters; structure as principle → mechanism → failure
- `testerEvidence`: direct quotes from tester reports; must cite at least one tester
- `fix`: exact correction — specific hex value, CSS custom property name, or Tailwind class; never a vague direction
- `page`: artifact section or component where the violation occurs

Return an `AgentOutput` as defined in `../types.md`. Every finding must be a structured `BrandFinding` object.
