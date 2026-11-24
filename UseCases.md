# Windows-MCP — Use Cases

> Describes how different actors interact with the system to achieve goals.
>
> Review and update after major feature changes. Add to WorkPlan.md as a task: "Review UseCases.md for accuracy."

**Last updated:** 2026-11-20 (reviewed against the 20 tools and CLI in `src/windows_mcp/` by the WorkPlan driver)

---

## Actors

| Actor | Description |
|-------|-------------|
| AI agent (MCP client) | Claude Desktop, Claude Code, Elysia or another MCP client that calls the tools to see and drive the PC |
| Operator (Bill) | Installs, configures and secures the server; decides which tools are enabled and whether HTTP access is allowed |
| Remote client | Another device (for example a Pi or phone) that reaches the server over streamable HTTP with an auth key |

---

## Use Cases

### UC-001: See the screen and act on a UI element

**Actor:** AI agent
**Preconditions:** Server running (stdio or HTTP); the target app is open.
**Trigger:** The agent needs to click, type or read something in a desktop app.

**Main Flow:**
1. Agent calls `Screenshot` for a fast view, or `Snapshot` when it needs element ids.
2. Server returns the image (capped to 1920x1080) plus window summary and, for Snapshot, interactive and scrollable elements with labels/ids.
3. Agent calls `Click`, `Type`, `Scroll`, `Move` or `Shortcut` with coordinates or a label/id.
4. Agent calls `WaitFor` (or `Wait`) until the UI reaches the expected state, then takes another Screenshot to confirm.

**Alternate Flows:**
- *A1 (browser window):* Snapshot detects Chrome/Edge/Firefox and adds DOM extraction.
- *A2 (element not found):* the tool returns an `isError` result and the agent re-snapshots.

**Postconditions:** The action is applied in the target app.

---

### UC-002: Launch or manage an application

**Actor:** AI agent
**Preconditions:** Server running.
**Trigger:** The agent needs an app opened, switched to or resized.

**Main Flow:**
1. Agent calls `App` with launch/switch/resize mode and the app name or window.
2. Server starts or focuses the window (virtual-desktop aware) and returns status.

**Postconditions:** The app window is open and focused at the requested size.

---

### UC-003: Run system operations without the UI

**Actor:** AI agent
**Preconditions:** Server running with `PowerShell`, `FileSystem`, `Registry` and/or `Process` enabled.
**Trigger:** A task is faster through the shell, files, registry or process list than through clicks.

**Main Flow:**
1. Agent calls `PowerShell`, `FileSystem` (read/write/copy/move/delete/list/search/info), `Registry` (get/set/delete/list) or `Process` (list/kill).
2. Server runs it with the user's rights and returns output or an error.

**Notes:** No sandbox. The operator can remove risky tools with `--exclude-tools PowerShell,Registry`.

---

### UC-004: Fetch web content

**Actor:** AI agent
**Trigger:** The agent needs the text of a web page.

**Main Flow:**
1. Agent calls `Scrape` with a URL.
2. Server fetches it over HTTP (SSRF-protected) and returns markdown; with `use_dom=True` it reads the open browser's DOM instead.

---

### UC-005: Serve a remote client securely

**Actor:** Operator, Remote client
**Preconditions:** Operator has set an auth key (`windows-mcp auth`) and optionally TLS.
**Trigger:** A device on the LAN needs to use the PC's tools.

**Main Flow:**
1. Operator runs `windows-mcp serve --transport streamable-http --host <ip> --port 8000` with an auth key, IP allowlist and optional TLS, or registers it with `windows-mcp install`.
2. Remote client connects with the bearer token (or OAuth client credentials).
3. Server checks auth and IP allowlist on every request.

**Alternate Flows:**
- *A1 (no auth on a non-loopback host):* the server refuses to start unless `--allow-insecure-remote` is given.

**Postconditions:** Only authenticated, allowlisted clients reach the tools.

---

### UC-006: Notify the user and share text

**Actor:** AI agent
**Main Flow:**
1. Agent calls `Notification` to show a Windows toast, or `Clipboard` to get or set text.

---

### UC-007: Restrict the tool set

**Actor:** Operator
**Main Flow:**
1. Operator starts the server with `--tools Screenshot,Click,...` (allow list) or `--exclude-tools ...` (deny list).
2. Only the selected tools are registered; `--tools` wins over `--exclude-tools`.
