# Changelog

All notable changes to Kuroko are documented here. Versions follow the
`MAJOR.MINOR.PATCH` scheme; a license is perpetual for its major version.

## 1.0.1 — 2026-07-16

### Fixed
- License activation now targets the live store product. 1.0.0 shipped wired
  to a pre-release (test-mode) product, so purchased license keys were
  rejected with "This license is for a different product" — 1.0.1 accepts
  them. If you bought a key and hit that message, update and activate again.
- The MCP server now reports the app version in its handshake (was a
  placeholder `0.1.0`).

## 1.0.0 — 2026-07-11

First public release.

### Added
- **MCP server** for AI agents to see and drive native macOS apps via the
  Accessibility API — semantic, background, no focus steal.
- Read/inspect tools: `list_apps`, `get_ui_tree`, `read_element`,
  `assert_visible`, `check_permissions`.
- Action tools: `tap`, `double_tap`, `input_text`, `press_key`, `scroll`,
  `screenshot` (background window capture), `launch_app`, `stop_app`.
- MCP resources: `guide://usage` (drive-and-verify workflow) and
  `policy://current` (live effective policy).
- **Companion app**: onboarding wizard, permissions dashboard, live activity
  log, connection instructions, and license management.
- **App policy**: default denylist of sensitive apps, disarmable write tools,
  and an optional allowlist mode.
- **Licensing**: 14-day trial and online license activation (up to 3 Macs per
  Individual license), with device deactivation to move a seat.
- Signed (Developer ID) & notarized direct-download DMG.
- In-app update check.
