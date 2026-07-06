---
name: Windows-MCP-assistant
description: Project-specific Claude skill for Windows-MCP. Use this skill for tasks specific to this project — architecture questions, code conventions, domain-specific workflows, debugging, feature work, or anything that requires deep project context. Triggers on any request that references Windows-MCP or work being done within this codebase. Always invoke when the user asks about this project.
---

# Windows-MCP Assistant

## Read these files at session start

1. `CLAUDE.md` — project architecture, conventions, context
2. `WorkPlan.md` — what's currently being worked on
3. `Training.md` → "Recommendations for Claude" — user preferences and efficiency notes
4. `Commands.md` (last session block) — what happened recently

---

## Agent Registry

This project has four agents. Engage them automatically in the background as work warrants — don't wait for the user to ask.

| Agent | Skill path | Role | Engage automatically when... |
|-------|-----------|------|------------------------------|
| **Primary (you)** | `project-skill/SKILL.md` | Coding, architecture, project coordination | Any project task |
| **Codie** | `codie/SKILL.md` | Peer code reviewer | Any coding task is complete |
| **Tester** | `tester/SKILL.md` | QA: smoke tests, functional tests, test case authoring | After Codie approves; after any bug fix; after a feature ships |
| **Doc** | `doc/SKILL.md` | Technical writer: specs, manuals, test reports, HTML/Word docs | After a feature ships; after a test run; when specs change; when user asks for docs |
| **Data** | `data/SKILL.md` | DBA / data analyst / data engineer (MySQL & SQLite) | Any feature involving data storage, queries, migrations, or reporting |

All agent skill paths are relative to `.claude/skills/` in the project root.

### Background engagement rules

Apply these without being asked:

- **After writing or changing code** → spawn Codie to review, then Tester to smoke test
- **After a feature is marked shipped in Features.md** → spawn Doc to update the help manual / release notes
- **After Tester completes a test run** → spawn Doc to generate a Test Summary Report if the run covered significant ground
- **After Specifications.md changes** → spawn Doc to update any formal spec documents in `docs/`
- **When a feature involves storing, querying, or reporting on data** → spawn Data to design the schema or write the queries
- **After Data writes a migration** → spawn Codie to review before running
- **If a TODO.md, todo.txt, or TASKS.md appears** → merge into WorkPlan.md and delete it (no agent needed, handle inline)
- **At session start** → check Commands.md last session block to understand context; no agents needed unless there's active work in flight

---

## Code completion workflow

When a coding task is complete, pipeline these agents in order — don't tell the user work is done until all three sign off:

1. **Codie** — peer code review
   - Fix any CHANGES REQUIRED, re-run Codie until **APPROVED**

2. **Tester** — smoke test + functional test
   - Run relevant test cases, document in TestCases.md
   - Must reach **READY**

3. **Doc** — update docs if behavior changed
   - Update Specifications.md, docs/ files as needed
   - Confirm docs are current

4. Report to user: "Done — reviewed by Codie, tested by Tester, docs updated by Doc."

---

## Before any git commit

1. Update `WorkPlan.md` — mark completed tasks done
2. Update `Features.md` — reflect features added or changed
3. Update `Specifications.md` — reflect any behavior changes
4. Confirm Doc has updated any affected docs in `docs/`
5. Commit

---

## Standing rules

- **One WorkPlan.** Merge any `TODO.md`, `todo.txt`, `TASKS.md` into `WorkPlan.md` and delete them.
- **Session logging.** Append significant user instructions and commands to `Commands.md` under today's session header.
- **Specs first.** Before writing code, check `Specifications.md`. Before writing docs, check the same. Never invent behavior.

---

## Common tasks for this project

[Add project-specific workflows here as they emerge]
