# Goals — Windows-MCP

> **Status:** ⚡ Active | **Last reviewed:** 2026-07-04
> **Upstream:** github.com/CursorTouch/Windows-MCP (fork)

---

## Project Overview

Windows-MCP is a Model Context Protocol server that gives AI agents (Claude) direct control
over the Windows desktop — mouse, keyboard, screenshots, clipboard, process management, and
more. It's the bridge between Claude's reasoning and the user's actual Windows environment.

**Why it exists:** To enable Claude (and other MCP-compatible AI agents) to autonomously operate
Windows applications on the user's behalf — automating workflows that can't be handled through
APIs alone. Within the FriesianLogic ecosystem, it's the desktop-control backbone for
Cortana and general Cowork automation.

---

## Success Criteria

- [ ] All core input tools (click, type, scroll, screenshot) work reliably on Windows 10/11
- [ ] MCP server connects cleanly to Claude Desktop and Cowork without configuration friction
- [ ] Actions execute with low enough latency to feel interactive (< 500ms for simple actions)
- [ ] Error messages are clear and actionable when something fails
- [ ] Upstream changes from CursorTouch/Windows-MCP are tracked and merged regularly

---

## Feature / Component Goals

### Desktop Control (Mouse & Keyboard)
Precise, reliable input simulation. Click, double-click, right-click, drag, type, hotkeys,
and scroll must work across all standard Windows applications.

### Screenshot & Visual Context
Capture the full screen or a region and return it to the AI agent. Should handle multiple
monitors and different DPI settings without distortion or clipping.

### Clipboard Integration
Read from and write to the Windows clipboard. Support both text and image clipboard contents.

### Process Management
List running processes, launch applications, and kill processes by name or PID. Must not
require elevated privileges for standard use cases.

### Window Management
Bring windows to foreground, resize, minimize, maximize. Essential for automation workflows
that involve switching between apps.

### Registry & Filesystem Access
Read registry keys and browse/read files on the local filesystem. Scoped carefully to avoid
unintended access.

---

## Non-Goals

- Not building a standalone automation product — this is infrastructure for AI agents
- Not supporting macOS or Linux (see other MCP servers for those platforms)
- Not a security tool — access controls are the host system's responsibility
- Not replacing RPA tools like Power Automate for enterprise workflows

---

## User Experiences

**Hands-free automation:** Bill says "open my calendar and check tomorrow's meetings" —
Cortana uses Windows-MCP to open the browser, navigate to Google Calendar, take a screenshot,
read the events, and speak them back.

**AI-assisted file work:** Claude uses Windows-MCP to locate a file, open it in the right app,
make an edit, and save — all from a natural language instruction, no manual navigation needed.

**Reliable foundation:** When something goes wrong, the error is surfaced clearly to the AI
agent (and through it, to the user) rather than silently failing or hanging.
