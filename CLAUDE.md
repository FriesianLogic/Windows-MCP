# CLAUDE.md

<!-- AUTO:CENTRALCLAUDE START -->
## Attached to the central Claude.md (no exceptions)

This project is attached to the central Claude.md: `E:\Dev\Claude\CLAUDE.md`, together with `E:\Dev\Claude\GlobalRules.md`. At the start of every session read those two files first and follow them (GR-20), then the rest of this file, which adds only project-specific rules. Every rule in them applies here in full. Resource parent directory: `E:\Dev` (GR-22). Do not point to any other location for the main Claude.md.

**Standard 3-step workflow on EVERY request (GR-57, no exceptions):** (1) CHECKLIST - first action: list every task in the message as a numbered checklist and work all of them to the end without stopping (GR-54). (2) HISTORY - before starting, read this project's `ChatHistory\ChatHistory.md` for open items, missed tasks and lessons and run anything missed in the background without asking (GR-55/56); append to it after each completed task. (3) VERIFY - before finishing, check each item against evidence and end with a pass/fail line per numbered item; list anything not done with a 'Fix It All' option.
<!-- AUTO:CENTRALCLAUDE END -->


This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Windows-MCP is a Python MCP (Model Context Protocol) server that bridges AI LLM agents with the Windows OS, enabling direct desktop automation. It exposes 20 tools (App, DisplayInventory, PowerShell, FileSystem, Snapshot, Screenshot, Click, Type, Scroll, Move, Shortcut, Wait, WaitFor, Scrape, MultiSelect, MultiEdit, Clipboard, Process, Notification, Registry) via FastMCP. Tool count verified against `src/windows_mcp/tools/*.py` on 2026-11-20.

## Build & Development Commands

```bash
uv sync                    # Install dependencies
uv run windows-mcp         # Run the MCP server
ruff format .              # Format code
ruff check .               # Lint code
ruff check --fix .         # Lint and auto-fix
pytest                     # Run all tests (if tests/ exists)
pytest tests/test_foo.py   # Run a single test file
```

**Package manager**: UV (not pip). **Python**: 3.12+ (`requires-python = ">=3.12"`; dev uses 3.13). **Build backend**: setuptools (`setuptools.build_meta`).

## Architecture

The codebase follows a layered service architecture under `src/windows_mcp/`:

**Entry point** — `__main__.py`: Click CLI group `main` with subcommands `serve` (run the server; transport stdio/sse/streamable-http, host/port, auth key, IP allowlist, CORS, TLS, OAuth, `--tools`/`--exclude-tools` filters, `--stateless-http`, `--debug`, `--config`), `install`/`uninstall` (register/remove a Windows startup task via schtasks/PowerShell) and `auth` (set up HTTP auth, optional self-signed TLS). `_build_mcp()` creates the FastMCP instance with an async lifespan that initializes Desktop, WatchDog and Analytics, then calls `tools.register_all()`.

**Tools package** — `tools/`: one module per tool group (`app`, `display`, `shell`, `filesystem`, `snapshot`, `input`, `scrape`, `multi`, `clipboard`, `process`, `notification`, `registry`), each exposing `register(mcp, get_desktop=..., get_analytics=...)`. `tools/__init__.py:register_all` registers them in order. Tools resolve the Desktop/Analytics singletons lazily and are wrapped with `@with_analytics` for telemetry.

**Service packages** — `filesystem/`, `registry/`, `process/`, `powershell/`, `notifications/`: thin services behind the matching tools. `infrastructure/`: `analytics.py` (PostHog), `auth.py` (bearer-token/OAuth middleware for HTTP transports), `oauth.py` (minimal OAuth 2.0 authorization server), `security.py` (SSRF protection and IP allowlist), `config.py`. `paths.py`: resolves Claude Desktop data directories for standard and MSIX (Store) installs.

**Desktop service** — `desktop/service.py`: High-level orchestrator. Manages window operations (launch, resize, switch), screenshots, mouse/keyboard actions, and clipboard. Interfaces with Tree service for UI element discovery. `desktop/views.py` defines data models: `DesktopState`, `Window`, `Size`, `BoundingBox`, `Status`.

**Tree service** — `tree/service.py`: Captures the Windows accessibility tree from active and background windows. Identifies interactive elements and scrollable areas. Uses `ThreadPoolExecutor` for multi-threaded UI traversal. `tree/views.py` defines `TreeElementNode`, `ScrollElementNode`, `TreeState`. `tree/config.py` has control type configurations.

**UIAutomation wrapper** — `uia/`: Low-level abstraction over the Windows UIAutomation COM API via `comtypes`. `core.py` wraps the main automation object, `controls.py` has control-specific logic, `patterns.py` wraps UIAutomation patterns, `enums.py` has COM enumerations, `events.py` handles event subscriptions.

**WatchDog** — `watchdog/service.py`: Runs in a separate thread monitoring UI focus changes via UIAutomation events. Notifies the Tree service of focus changes so the accessibility tree stays current.

**Virtual Desktop Manager** — `vdm/core.py`: Tracks which windows belong to which Windows virtual desktop (Win10/11).

**Analytics** — `infrastructure/analytics.py`: Optional PostHog telemetry (disabled with `ANONYMIZED_TELEMETRY=false` env var). Tracks tool names and errors only, not arguments or outputs.

## Code Style

- Formatter/linter: **Ruff** (line length 100, double quotes)
- Naming: PEP 8 — `snake_case` functions/variables, `PascalCase` classes, `UPPER_CASE` constants
- Type hints required on function signatures
- Google-style docstrings for public functions/classes

## Key Design Details

- Screenshots are capped to 1920x1080 for token efficiency
- Mouse/keyboard input uses UIA (same coordinate space as BoundingRectangle; no DPI mismatch)
- Screenshot is the preferred fast visual-context tool; Snapshot is the heavier path for UI element ids and DOM extraction
- Browser detection (Chrome, Edge, Firefox) triggers special DOM extraction mode in Snapshot
- Fuzzy string matching (`thefuzz`) is used for element name matching
- UI element fetching has retry logic (`THREAD_MAX_RETRIES=3` in tree service)
- The server supports stdio, SSE, and streamable HTTP transports

## Environment Variables

| Variable | Default | Description |
|---|---|---|
| `WINDOWS_MCP_SCREENSHOT_SCALE` | `1.0` | Scale factor for screenshots (range `0.1`–`1.0`). Lower on 1440p/4K to stay under Claude Desktop's 1 MB limit. Resolved in `tools/_snapshot_helpers.py`. |
| `WINDOWS_MCP_SCREENSHOT_BACKEND` | `auto` | Screenshot backend: `auto`, `dxcam`, `mss`, `pillow`. Resolved in `desktop/screenshot.py`. |
| `WINDOWS_MCP_PROFILE_SNAPSHOT` | _(off)_ | Set to `1`/`true`/`yes`/`on` to log per-stage timing for Screenshot/Snapshot. Checked in `tools/_snapshot_helpers.py` and `desktop/service.py`. |
| `ANONYMIZED_TELEMETRY` | `true` | Set to `false` to disable PostHog telemetry. Checked in `__main__.py` and `analytics.py`. |
| `POSTHOG_API_KEY` | Project default | Override the PostHog project write key used for anonymous telemetry. Set to an empty string to skip PostHog client initialization. Checked in `analytics.py`. |
| `POSTHOG_HOST` | `https://us.i.posthog.com` | Override the PostHog host for anonymous telemetry, such as for a self-hosted PostHog deployment. Checked in `analytics.py`. |
| `WINDOWS_MCP_DEBUG` | `false` | Set to `1`/`true`/`yes`/`on` to enable debug mode. Checked in `config.py`. Also available as `--debug` CLI flag. |
| `WINDOWS_MCP_DISABLE_FLASH` | _(off)_ | Set to `1`/`true`/`yes`/`on` to suppress the orange-red glowing border that briefly appears after every screenshot. Resolved in `desktop/flash_overlay.py`. |

## Security Context

This server has **full system access** with no sandboxing. Tools like Shell and App can perform irreversible operations. The recommended deployment target is a VM or Windows Sandbox.
