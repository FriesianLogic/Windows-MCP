# Windows-MCP — Test Cases

> Test cases derived from code, features, and use cases.
>
> **Auto-generation:** When Claude adds or changes features, it should propose new test cases here and add "Review TestCases.md" to WorkPlan.md. Review and update after major changes.

**Last updated:** 2026-11-20 (reviewed by the WorkPlan driver: one TC per automated test file in `tests/`, feature ids from Specifications.md)

---

## Test Coverage Summary

| Area | Test files | Test functions | Passing | Failing | Not run |
|------|-----------|----------------|---------|---------|---------|
| All automated (pytest, `tests/`) | 28 | 341 | — | — | 341 |

**Run status (2026-11-20):** not run. An offline run through the Test Manager (TM-195, TM-197) failed before collection because the dependencies (ruff, posthog, ...) are not in the local uv cache and no `.venv` was installed. To run: `uv sync --extra dev` (downloads from PyPI), then the Test Manager command in Commands.md.

**Run rule:** always run through the Test Manager (GR-41/43): `E:\Python\Python314\python.exe E:\Dev\DevOps\testmanager\tm.py run --project Windows-MCP --what "unit tests" -- uv run --extra dev pytest -q`

---

## Automated test cases

| TC | File | Feature (Specifications.md) | What it covers | Type | Tests | Pass/Fail |
|----|------|------------------------------|----------------|------|-------|-----------|
| TC-001 | `tests/test_analytics.py` | Analytics / telemetry | PostHog init, opt-out via ANONYMIZED_TELEMETRY, key/host override | Unit | 7 | ⬜ Not run |
| TC-002 | `tests/test_app_tool.py` | F-001 App | launch/switch/resize window handling | Unit | 9 | ⬜ Not run |
| TC-003 | `tests/test_cli_legacy_flags.py` | F-015 CLI serve | legacy top-level flags rejected or mapped to `serve` | Unit | 5 | ⬜ Not run |
| TC-004 | `tests/test_cli_paired_flags.py` | F-015 CLI serve | paired flags (TLS cert/key, OAuth id/secret) validated together | Unit | 5 | ⬜ Not run |
| TC-005 | `tests/test_config_stateless.py` | F-015 CLI serve | `server.stateless_http` config knob | Unit | 9 | ⬜ Not run |
| TC-006 | `tests/test_config_validation.py` | Config | config file validation | Unit | 3 | ⬜ Not run |
| TC-007 | `tests/test_desktop_utils.py` | Desktop service | desktop helper utilities | Unit | 9 | ⬜ Not run |
| TC-008 | `tests/test_desktop_views.py` | Data model | DesktopState / Window / BoundingBox views | Unit | 16 | ⬜ Not run |
| TC-009 | `tests/test_deterministic_drag_core.py` | F-007 Move (drag) | deterministic drag, UIA core | Unit | 6 | ⬜ Not run |
| TC-010 | `tests/test_deterministic_drag_desktop.py` | F-007 Move (drag) | deterministic drag, Desktop layer | Unit | 5 | ⬜ Not run |
| TC-011 | `tests/test_deterministic_drag_input.py` | F-007 Move (drag) | deterministic drag, input tool layer | Unit | 9 | ⬜ Not run |
| TC-012 | `tests/test_display_inventory_tool.py` | F-002 DisplayInventory | display layout and DPI report | Unit | 4 | ⬜ Not run |
| TC-013 | `tests/test_filesystem_service.py` | F-004 FileSystem | read/write/copy/move/delete/list/search/info service | Unit | 34 | ⬜ Not run |
| TC-014 | `tests/test_filesystem_views.py` | F-004 FileSystem | file system result views and formatting | Unit | 18 | ⬜ Not run |
| TC-015 | `tests/test_flash_overlay.py` | F-006 Screenshot | flash border overlay and WINDOWS_MCP_DISABLE_FLASH | Unit | 16 | ⬜ Not run |
| TC-016 | `tests/test_iserror_compliance.py` | All tools | failures return isError results | Unit | 3 | ⬜ Not run |
| TC-017 | `tests/test_multi_tools.py` | F-010 MultiSelect / MultiEdit | multi-click and multi-field entry | Unit | 3 | ⬜ Not run |
| TC-018 | `tests/test_paths.py` | paths.py | Claude Desktop data dir resolution (standard and MSIX) | Unit | 9 | ⬜ Not run |
| TC-019 | `tests/test_registry.py` | F-014 Registry | get/set/delete/list | Unit | 21 | ⬜ Not run |
| TC-020 | `tests/test_screenshot_capture.py` | F-005/F-006 Screenshot | capture backends (auto/dxcam/mss/pillow), scaling, size cap | Unit | 42 | ⬜ Not run |
| TC-021 | `tests/test_snapshot_display_filter.py` | F-005 Snapshot | display filter for multi-monitor snapshots | Unit | 22 | ⬜ Not run |
| TC-022 | `tests/test_startup_task.py` | F-016 CLI install/uninstall | startup task script and schtasks registration | Unit | 3 | ⬜ Not run |
| TC-023 | `tests/test_tree_ia2.py` | Tree service | IAccessible2 / MSAA traversal helpers | Unit | 37 | ⬜ Not run |
| TC-024 | `tests/test_tree_service.py` | Tree service | accessibility tree capture | Unit | 13 | ⬜ Not run |
| TC-025 | `tests/test_tree_views.py` | Data model | TreeElementNode / ScrollElementNode views | Unit | 16 | ⬜ Not run |
| TC-026 | `tests/test_wait_for_tool.py` | F-008 WaitFor | polling until a UI condition is met | Unit | 8 | ⬜ Not run |
| TC-027 | `tests/test_watchdog_recovery.py` | WatchDog | self-healing reconnect (issue #332) | Unit | 6 | ⬜ Not run |
| TC-028 | `tests/test_watchdog_toggle.py` | WatchDog | WINDOWS_MCP_WATCHDOG opt-out (issue #332) | Unit | 3 | ⬜ Not run |

---

## Manual test cases (no automated coverage yet)

### TC-101: Screenshot to click round trip (UC-001)

**Type:** Manual / E2E  **Priority:** High
**Steps:** 1. Start `uv run windows-mcp serve`. 2. From an MCP client call `Screenshot`, then `Snapshot` on Notepad. 3. `Click` the text area by label, `Type` "hello", `WaitFor` the text to appear.
**Expected Result:** Text appears in Notepad; every call returns without `isError`.
**Pass/Fail:** ⬜ Not run

### TC-102: Remote HTTP refuses to start without auth (UC-005)

**Type:** Manual  **Priority:** High
**Steps:** 1. Run `uv run windows-mcp serve --transport streamable-http --host 0.0.0.0 --port 8000` with no auth key.
**Expected Result:** Exits with "Refusing to bind HTTP transport ... without authentication".
**Pass/Fail:** ⬜ Not run

### TC-103: Tool allow list (UC-007)

**Type:** Manual  **Priority:** Med
**Steps:** 1. Run `serve --tools Screenshot,Click`. 2. List tools from the client.
**Expected Result:** Only Screenshot and Click are listed.
**Pass/Fail:** ⬜ Not run

<!-- Claude: when generating test cases from code or features, follow the TC-XXX pattern above.
     Auto-generate test cases when:
     - A new feature is marked as shipped in Features.md
     - A significant code change is described in Commands.md
     - The user asks "write test cases for X"

     After adding cases here, add a WorkPlan task: "Review and run TestCases.md" with Medium priority. -->
