# Windows-MCP — Work Plan

> **Single source of truth for all tasks.** Any TODO files, task lists, or notes with action items should be merged here and the originals deleted.
>
> ⚠️ **Update this file before every `git commit` or push.**

## Status: 🟡 In Planning

---

## Milestones

| # | Milestone | Target Date | Status |
|---|-----------|-------------|--------|
| 1 | Project setup & scaffolding | 2026-07-03 | ✅ Done |
| 2 | [Next milestone] | [Date] | ⬜ Not started |

---

## Active Tasks

| Task | Priority | Status | Notes |
|------|----------|--------|-------|
| Fill in CLAUDE.md architecture section | High | ⬜ | |
| Fill in Commands.md with real project commands | High | ⬜ | |
| Update Specifications.md after first code is written | High | ⬜ | |
| Review and update UseCases.md | Med | ⬜ | After major changes |
| Review and update TestCases.md | Med | ⬜ | After major changes |

---

## Backlog

- [ ] [Future task]

---

## Completed

- [x] Project scaffolding and initial file structure (2026-07-03)

---

## Rules

- There is **one** WorkPlan.md. Do not create separate TODO, TASKS, or checklist files.
- Mark tasks done here before every commit.
- After major feature work, add tasks to review UseCases.md and TestCases.md.
- Codie (peer reviewer) must approve all code before tasks are marked ✅ Done.

## Command log - 2026-11-09 (ProjectInfo global-rules sync)

| Date | Command | Result |
|---|---|---|
| 2026-11-09 | "go through every project in Dev, and make sure the ProjectInfo knows about the global rules and all things in E:\Dev\Claude\CLAUDE.md" (the central Claude.md); then: "correct my actions everywhere to point to the proper main Claude.md in all projects"; "every project should be attached to the central Claude.md file - no exceptions"; "that is a standard rule on every project and ensure it done also when scaffolding" (saved as global rule GR-51; newproject skill updated); "remember that" (saved to Claude memory) | Added/refreshed the "Global rules and Claude workspace conventions" block in ProjectInfo.md (GR rules + E:\Dev\Claude\CLAUDE.md rules); ProjectInfo.html re-rendered where auto-generated. Backups: *.bak-20261109-grinfo. Script: E:\Dev\DevOps\allprojects\sync-projectinfo-globalrules.py |
