# Windows-MCP — Specifications

> Describes what the system does, how it behaves, and what constraints it operates under.
>
> **Keep this current as code changes.** When you commit, check whether any behavior changed and update here first.

**Last updated:** 2026-11-20 (filled in from the code by the WorkPlan driver; source: `src/windows_mcp/`, `pyproject.toml` v0.8.2)

---

## Overview

**Project:** Windows-MCP (fork of CursorTouch/Windows-MCP, package `windows-mcp` 0.8.2)
**Description:** MCP server for Windows desktop control. Used by Claude and other MCP clients to see the desktop and drive it with mouse, keyboard, PowerShell, file system, registry and process tools.
**Language/Framework:** Python 3.12+ (dev on 3.13), FastMCP 3.x, Windows UIAutomation via comtypes, Click CLI. Package manager uv, build backend setuptools.

---

## Functional Specifications

### Core Capabilities (MCP tools, registered in `tools/__init__.py`)

| # | Tool | Description | Status |
|---|------|-------------|--------|
| F-001 | App | Launch apps, switch, resize and manage windows | Done |
| F-002 | DisplayInventory | Report display layout, device names and DPI metadata | Done |
| F-003 | PowerShell | Run PowerShell commands and return output | Done |
| F-004 | FileSystem | Read, write, list, search, copy, move, delete files and folders (eight modes) | Done |
| F-005 | Snapshot | Screenshot plus accessibility tree: interactive elements with ids, scrollable areas, optional DOM extraction for Chrome/Edge/Firefox | Done |
| F-006 | Screenshot | Fast screenshot-first snapshot (cursor, desktop/window summary, image) without UI tree extraction | Done |
| F-007 | Click / Type / Scroll / Move / Shortcut | Mouse and keyboard input by coordinates or by UI element label/id; key combinations | Done |
| F-008 | Wait / WaitFor | Fixed pause, or poll the accessibility tree until a UI condition is met | Done |
| F-009 | Scrape | Fetch a web page (lightweight HTTP, or browser DOM with `use_dom=True`) and return markdown | Done |
| F-010 | MultiSelect / MultiEdit | Multiple clicks/selections (optional Ctrl) and multiple text-field entries in one call | Done |
| F-011 | Clipboard | Get or set clipboard text | Done |
| F-012 | Process | List processes (CPU/memory) and kill by PID or name | Done |
| F-013 | Notification | Send a Windows toast notification | Done |
| F-014 | Registry | Get, set, delete and list registry values/keys | Done |
| F-015 | CLI `serve` | Run over stdio (default), SSE or streamable HTTP; tool allow/deny lists (`--tools`, `--exclude-tools`) | Done |
| F-016 | CLI `install` / `uninstall` | Register or remove a Windows startup task that runs `serve` | Done |
| F-017 | CLI `auth` | Set up HTTP bearer auth, optional self-signed TLS | Done |

### Inputs & Outputs

| Flow | Input | Output | Notes |
|------|-------|--------|-------|
| MCP tool call | JSON-RPC tool call over stdio / SSE / streamable HTTP | Text, structured JSON or image content; errors flagged `isError` | `tests/test_iserror_compliance.py` |
| Screenshot / Snapshot | Optional display filter, scale | PNG image capped to 1920x1080, scaled by `WINDOWS_MCP_SCREENSHOT_SCALE` | Backend auto/dxcam/mss/pillow |
| Telemetry | Tool name and error type | PostHog event | Never arguments or outputs; off with `ANONYMIZED_TELEMETRY=false` |

### Business Rules

- **Full system access:** tools run with the user's rights and no sandbox; recommended target is a VM or Windows Sandbox.
- **Remote HTTP needs auth:** binding to a non-loopback host requires an auth key or OAuth unless `--allow-insecure-remote` is given.
- **Explicit tool list wins:** `--tools` overrides `--exclude-tools`.
- **Coordinates:** input uses UIA coordinates (same space as BoundingRectangle), so no DPI mismatch.

---

## Non-Functional Specifications

| Attribute | Requirement | Notes |
|-----------|-------------|-------|
| Performance | Screenshot is the fast path; Snapshot does multi-threaded tree traversal with up to 3 retries per element fetch | `WINDOWS_MCP_PROFILE_SNAPSHOT=1` logs per-stage timing |
| Image size | Stay under Claude Desktop's 1 MB image limit | Lower `WINDOWS_MCP_SCREENSHOT_SCALE` on 1440p/4K |
| Security | Bearer token / OAuth for HTTP, IP allowlist, CORS origins, TLS, SSRF protection on outbound fetches | `infrastructure/auth.py`, `oauth.py`, `security.py` |
| Platform | Windows 10/11 only | UIAutomation COM, pywin32 |

---

## System Boundaries

**In scope:**
- Seeing and driving the local Windows desktop for an MCP client.
- Running PowerShell, file system, registry and process operations on the local machine.

**Out of scope:**
- Sandboxing or permission prompts (the client or host must provide these).
- Non-Windows platforms.

---

## Dependencies & Integrations

| Dependency | Version / Endpoint | Purpose |
|-----------|-------------------|---------|
| fastmcp | >=3.0 | MCP server and transports |
| comtypes, pywin32 | >=1.4.15, >=311 | UIAutomation COM and Win32 APIs |
| dxcam, pillow | >=0.3.0, >=11.2.1 | Screen capture and image processing |
| thefuzz, fuzzywuzzy, python-levenshtein | see pyproject | Fuzzy matching of UI element names |
| click | >=8.2.1 | CLI |
| markdownify, requests | see pyproject | Scrape tool |
| psutil | >=7.0.0 | Process tool |
| posthog | >=7.4.0 | Optional anonymous telemetry |

---

## Data Model (summary)

| Entity | Key Fields | Notes |
|--------|-----------|-------|
| DesktopState | active window, windows, cursor, tree state, screenshot | `desktop/views.py` |
| Window | name, handle, status, bounding box, virtual desktop | `desktop/views.py` |
| TreeElementNode / ScrollElementNode | label/id, control type, name, bounding box | `tree/views.py` |

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-07-03 | Initial specifications created | Bill McCracken |
| 2026-11-20 | Filled in from code: 20 tools, CLI commands, I/O, rules, NFRs, dependencies, data model | WorkPlan driver (Claude) |
