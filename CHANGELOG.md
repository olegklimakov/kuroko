# Changelog

All notable changes to Kuroko are documented here. Versions follow the
`MAJOR.MINOR.PATCH` scheme; a license is perpetual for its major version.

## 1.0.3 — 2026-08-19

### Added
- **Automatic updates.** Kuroko now updates itself: it asks once, on first
  launch, whether to check automatically, then checks daily and installs the
  new version in place. Every update is cryptographically verified before it
  is applied, and you can always check by hand from Settings. Copies up to
  1.0.2 could only show a banner pointing at the download page — this is the
  last update you have to install manually.
- **One-click setup for agent hosts.** The Connect tab now lists every host
  found on your Mac — Claude Code, Claude Desktop, Codex & ChatGPT Desktop,
  Cursor, Windsurf, VS Code — each with its live status and one button that
  adds or removes Kuroko's MCP entry for you. Hosts that aren't installed sink
  to the bottom of the list, and the manual JSON is still there for anything
  else that speaks MCP.
- **The skill installs with the entry.** Connecting Kuroko to a host that reads
  skills (Claude Code, Codex) now also installs the `kuroko` skill, which
  teaches the agent the see → find → act → verify workflow. Without it a
  connected agent tends to reason about your app instead of driving it.
  Removing the host's entry takes the skill back out.

### Changed
- Kuroko edits agent-host configs instead of rewriting them: where a host ships
  a CLI (Claude Code, Codex) the change goes through it, and everything else is
  written atomically, keeping the file's own permissions, following symlinks a
  dotfile manager may have put there, and leaving a `.kuroko-backup` of the
  original the first time it is touched. A config that can't be parsed is
  reported as an error, never overwritten.

## 1.0.2 — 2026-08-04

### Fixed
- Two tool calls arriving at once could garble each other's replies, leaving
  the agent waiting for an answer that never came — a session could appear to
  hang for many minutes. Replies now go out one at a time.
- A screenshot could take the whole server down with it when macOS's screen
  capture service got stuck. Captures now run in a separate short-lived
  process with a deadline, so a stuck one fails with a clear message instead
  of hanging the session.
- Overlapping screenshots no longer race the capture service into "Failed to
  start stream due to audio/video capture failure" — they are queued and run
  back to back.

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
