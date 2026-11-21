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
| Fill in CLAUDE.md architecture section | High | [~] built, awaiting Bill's verification | 2026-11-20 driver: section existed but was stale; corrected tool count 16 -> 20 (verified vs `src/windows_mcp/tools/*.py`), build backend Hatchling -> setuptools, Python 3.12+, added CLI subcommands (serve/install/uninstall/auth), `tools/` package, service packages, `infrastructure/` (auth/oauth/security/analytics), `paths.py`. Backup CLAUDE.md.bak-20261120-driver |
| Fill in Commands.md with real project commands | High | [~] built, awaiting Bill's verification | 2026-11-20 driver: added 'Project commands' reference table (uv sync, serve stdio/HTTP/SSE, --tools/--exclude-tools, --debug, auth, install/uninstall, ruff, Test Manager pytest) verified against pyproject.toml and `__main__.py` options; session entry 2026-11-20 |
| Update Specifications.md after first code is written | High | [~] built, awaiting Bill's verification | 2026-11-20 driver: template replaced with real spec: F-001..F-017 (20 tools + CLI), I/O, business rules (no sandbox, remote HTTP needs auth per `__main__.py` L614-625, --tools wins), NFRs, dependencies from pyproject, data model, change log |
| Review and update UseCases.md | Med | [~] built, awaiting Bill's verification | 2026-11-20 driver: template replaced with 3 actors and UC-001..UC-007 (see/act, launch app, system ops, scrape, secure remote HTTP, notify/clipboard, restrict tools) |
| Review and update TestCases.md | Med | [~] built, awaiting Bill's verification | 2026-11-20 driver: TC-001..TC-028 = one per test file in `tests/` (341 test functions, counted by regex) mapped to feature ids, plus manual TC-101..103. Pass/fail not run: see the [b] item below |

---

## Backlog

- [b] Run the unit test suite and record pass/fail in TestCases.md - blocker: needs Bill's OK to download the dependencies from PyPI (`uv sync --extra dev`); offline runs TM-195 and TM-197 on 2026-11-20 failed because ruff/posthog are not in the local uv cache. (added 2026-11-20 by the WorkPlan driver)
- 2026-11-20: removed the scaffold placeholder line "[ ] [Future task]" (WorkPlan cleanup by the driver; nothing real was in it).

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
| GR-50 global-rules recheck (gr53-rollout) 2026-11-14 19:06 | **Done.** first read: 53 rules recorded |
| GR-50 global-rules recheck (gr54-56-rollout) 2026-11-14 19:26 | **Done.** new GR-54, GR-55, GR-56; files edited: GlobalRules.md, AGENTS.md, CLAUDE.md -> APPLY to the project now |
| GR-50 global-rules recheck (gr57-rollout) 2026-11-14 20:52 | **Done.** new GR-57; files edited: GlobalRules.md, AGENTS.md, CLAUDE.md -> APPLY to the project now |
- 2026-11-20 22:27 TEST RUN: TM-195 started: WorkPlan driver: offline unit test suite (pytest) to populate TestCases.md (ports n/a, launcher n/a)
- 2026-11-20 22:27 TEST RUN: TM-195 ended: finished, exit code 1
- 2026-11-20 22:27 TEST RUN: TM-197 started: WorkPlan driver: offline unit test suite (pytest, retry without ruff) (ports n/a, launcher n/a)
- 2026-11-20 22:27 TEST RUN: TM-197 ended: finished, exit code 1

## WorkPlan driver log - 2026-11-20

- 2026-11-20 WorkPlan driver (GR-65/GR-68): read CLAUDE.md, WorkPlan.md, ChatHistory.md. Worked 6 open items: 5 Active Tasks set to [~] (CLAUDE.md architecture, Commands.md, Specifications.md, UseCases.md, TestCases.md), backlog placeholder removed. Added 1 [b] item (unit test run needs Bill's OK to download dependencies).
- 2026-11-20 Test runs TM-195 / TM-197 (offline pytest via tm.py) failed before collection: dependencies not in local uv cache; no download made. Side effect: an empty `.venv` (160 KB, gitignored) was created by uv; left in place for the later `uv sync`.
- 2026-11-20 Backups of every edited file: *.bak-20261120-driver. Nothing committed or pushed.

