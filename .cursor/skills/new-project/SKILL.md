# New Project Workspace

Create a new one-off project workspace in the correct flat project structure.

## Placement Rules

- Project folder: `process/projects/<project-name>/`
- Always create three subfolders: `agents/`, `prompts/`, and `specs/`
- Never create a README.md
- Agent files always go in `agents/` — never in the project root
- `prompts/` for one-off or run prompts; `specs/` for specs and plans
- Promote to `process/library/` only when an agent is reused across multiple projects

## Governance Checks

- No universal/library files placed under `process/projects/`
- No project files placed under `process/library/`
- Any reusable agent candidate is explicitly called out for promotion
- Do not create or modify global cross-domain route metadata

## Output Template

```
Project: [name]
Path: process/projects/[name]/
Created files: [list]
Promotion candidates: [optional list]
Validation checks:
- [ ] agents/, prompts/, and specs/ subfolders created
- [ ] No README.md created
- [ ] Agent files placed in agents/ subfolder
- [ ] Library/project boundaries preserved
```
