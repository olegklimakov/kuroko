---
name: kuroko
description: Drive and verify native macOS apps through the kuroko MCP server (Accessibility-based, background, no focus steal). Use when the user wants to inspect, click, type into, screenshot, or verify UI state of a native macOS app (their app under development, Finder, System apps, etc.) — triggers like "check my mac app", "tap the button in <app>", "verify the screen shows X", "drive <app> and confirm Y", "screenshot <app>'s window".
---

# kuroko — driving native macOS apps

You have (or the user has connected) the **kuroko** MCP server. It exposes
native macOS apps' Accessibility trees and lets you act on them **in the
background** — no window raising, no cursor movement, no focus steal. Prefer it
over pixel-based computer-use for native macOS apps.

If the tools aren't available, tell the user to connect the server:
`claude mcp add kuroko -- /path/to/kuroko mcp` (see the project README).

## The loop: see → find → act → verify

1. **Discover** the bundle id with `list_apps`. `launch_app` it if not running
   (it waits until the app is ready before returning).
2. **See** with `get_ui_tree` (semantic — roles, labels, ids, frames). Use
   `screenshot` only when you need pixels for a visual judgment.
3. **Act** with `tap` / `double_tap` / `input_text` / `press_key` / `scroll`.
4. **Verify** — never assume an action worked. Confirm with `assert_visible` or
   another `get_ui_tree`.

## Selecting elements

Target by one or more of (all must match): `id` (exact identifier — **most
reliable, prefer it**), `label` (case-insensitive substring), `role` (e.g.
`AXButton`), `subrole` (e.g. `AXSecureTextField`). When unsure, `get_ui_tree`
first and read the real ids/roles/labels instead of guessing.

## Keeping the tree small

`get_ui_tree` is node-budgeted; large windows return a `[truncated…]` marker.
Use `format: "outline"` for a compact text tree, lower `max_depth`, or
`read_element` a single element. Use `focused_window_only: false` to see the
menu bar and other windows.

## Keyboard & scrolling

- `press_key` takes named keys (return, tab, escape, arrows, space, delete,
  home, end, pageup, pagedown) **or a single letter/digit** + modifiers:
  `key:"s", modifiers:["command"]` = ⌘S, `key:"f", modifiers:["command"]` = ⌘F.
- If a target is scrolled off-screen, a tap is refused. `scroll` first (negative
  `delta_y` scrolls down), then retry.

## Guardrails you must respect

- **Permissions:** if tools fail with "not trusted", call `check_permissions`.
  The grant must be on the app running the server (the user's terminal / host),
  **not** on Kuroko.app. Tell the user to grant Accessibility to that host.
- **Policy:** sensitive apps (Terminal, Keychain, 1Password, System Settings)
  are blocked by default; the user may run a strict allowlist. If you see
  "Blocked by policy", read the `policy://current` resource and **surface it to
  the user** — do not try to work around it.
- **Passwords:** `input_text` refuses `AXSecureTextField`. Never attempt to enter
  credentials; ask the user to do it.
- Every tool call is logged (typed text by length only). Assume the user can
  audit what you did.

For deeper detail the server also exposes a `guide://usage` resource — read it if
you need more than this summary.
