# Windows-MCP — Session Command Log

> This file is a running log of instructions and commands given to Claude (in chat) or typed on the command line. It exists so Claude can resume context after a disconnect or new session.
>
> **Claude should append to this file** whenever the user gives a significant instruction, makes a decision, or runs a notable command. Use it to answer "what was I doing last time?"

---

## How to read this file

- Each session gets a `## Session — YYYY-MM-DD` header
- Entries are timestamped and labeled: `[CHAT]` for things said to Claude, `[CMD]` for terminal commands
- When starting a new session, read the last session block first to restore context

---

## Project commands (reference, verified against pyproject.toml and __main__.py on 2026-11-20)

Run from `E:\Dev\Windows-MCP`. Package manager is uv (not pip).

| Purpose | Command |
|---|---|
| Install dependencies (incl. dev tools) | `uv sync --extra dev` |
| Run server (stdio, default) | `uv run windows-mcp serve` |
| Run server over HTTP | `uv run windows-mcp serve --transport streamable-http --host 127.0.0.1 --port 8000` |
| Run server over SSE | `uv run windows-mcp serve --transport sse --port 8000` |
| Limit tools | `uv run windows-mcp serve --tools Screenshot,Click` / `--exclude-tools PowerShell,Registry` |
| Debug logging | `uv run windows-mcp serve --debug` (or `WINDOWS_MCP_DEBUG=1`) |
| Set up HTTP auth (optional self-signed TLS) | `uv run windows-mcp auth --help` |
| Register Windows startup task | `uv run windows-mcp install --transport streamable-http --port 8000` |
| Remove startup task | `uv run windows-mcp uninstall` |
| Format / lint | `uv run ruff format .` / `uv run ruff check .` (`--fix` to auto-fix) |
| Unit tests (always through the Test Manager, GR-41) | `E:\Python\Python314\python.exe E:\Dev\DevOps\testmanager\tm.py run --project Windows-MCP --what "unit tests" -- uv run --extra dev pytest -q` |
| Single test file | `uv run pytest tests/test_registry.py -q` |

Note: the tests need `uv sync --extra dev` first (downloads packages from PyPI). As of 2026-11-20 the local uv cache does not hold them, so an offline run fails (TM-195, TM-197).

---

## Session — 2026-07-03

**Project initialized.** Scaffolding created with NewProject skill.

> [CHAT] Set up new project: Windows-MCP

---

## Session — 2026-11-20

> [CHAT] WorkPlan driver (GR-65/GR-68): filled in the project-commands reference above, CLAUDE.md architecture, Specifications.md, UseCases.md, TestCases.md.
> [CMD] $ tm.py run ... -- uv run --offline --extra dev pytest -q  (failed: packages not in uv cache, no download made)

---

<!-- Claude: append new sessions below using this format:

## Session — YYYY-MM-DD

> [CHAT] User said: "..."
> [CMD] $ command run
> [CHAT] User said: "..."

Add a brief note if a decision was made or direction changed.
-->
