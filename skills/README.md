# Code Stacker Skills

These skill files are reusable instructions for AI coding sessions.

They are intentionally written for founders and pure vibe coders who want the AI to perform the engineering work without giving the model permission to roam across the whole application.

Use the skill that matches the current job.

| Skill | Purpose |
|---|---|
| [01 Scope a Function](01-scope-function/SKILL.md) | Turn plain-English product intent into one bounded `FUNCTION.md` contract. |
| [02 Build a Function](02-build-function/SKILL.md) | Implement exactly one function without modifying sibling capabilities. |
| [03 Prove Runtime](03-prove-runtime/SKILL.md) | Prove the real canonical path works instead of accepting a test-only success. |
| [04 Write Handoff](04-write-handoff/SKILL.md) | Update `DESIGN.md` so the next fresh session understands the actual code. |
| [05 Build Integration](05-build-integration/SKILL.md) | Build a reusable API/MCP/CLI/model/storage integration boundary. |
| [06 Compose Workflow](06-compose-workflow/SKILL.md) | Connect existing functions into a workflow without moving business logic into the pipeline. |
| [07 Map UI to Workflows](07-map-ui/SKILL.md) | Turn screens, buttons, forms, and dashboard actions into workflow entry points. |

## Suggested order for a new feature

```text
01 scope function
      ↓
05 build integration (only if needed)
      ↓
02 build function
      ↓
03 prove runtime
      ↓
04 write handoff
      ↓
06 compose workflow
      ↓
07 map UI
```

Not every feature needs every skill.
