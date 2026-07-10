# Troubleshooting

## Every tool fails with "not trusted" / Accessibility errors

**This is the most common problem.** macOS grants Accessibility **per process**,
to the exact signed binary that asks. The agent runs the Kuroko server as a child
of your **terminal / agent host** (Terminal, iTerm, VS Code, Claude Desktop, …),
so *that* app needs the Accessibility grant — **not** `Kuroko.app`.

You can have Kuroko's dashboard fully green and still have every agent call fail,
because the dashboard reflects the GUI's own process.

**Fix:**

1. Ask the agent to call `check_permissions` — it reports the *server process's*
   real status.
2. If `accessibility` is `false`, open **System Settings → Privacy & Security →
   Accessibility** and enable the app that launches your agent (your terminal /
   host), then retry. No restart needed.
3. If you connect from several hosts, each one needs its own grant.

## macOS asks to "bypass the private window picker" / allow screen recording

On **macOS 15 (Sequoia) and later**, the first time Kuroko captures a window (and
periodically after that) the system shows a prompt asking to allow direct screen
and audio access, attributed to the app that launched the server (your terminal /
agent host). **This is expected** — Kuroko captures a specific window directly
rather than going through the system picker. Click **Allow**. macOS re-asks
periodically as a privacy safeguard; it is not an error in Kuroko.

## `screenshot` fails or returns a blank/black image

- Screen Recording is a **separate** permission, also per process — grant it to
  the same host that runs the Kuroko server (System Settings → Privacy & Security
  → Screen Recording).
- If the app keeps a hidden/off-screen window, capture may pick it. Pass a
  `title` substring to disambiguate, or bring the intended window forward.

## A call returns "Blocked by policy"

The target app is on the denylist (a sensitive built-in like Terminal/Keychain/
1Password, or one you added), or you're in `allowlist` mode and it isn't listed,
or write tools are disarmed. See [policy.md](policy.md). Agents can read the live
policy via the `policy://current` MCP resource.

## `get_ui_tree` output is huge or ends with `[truncated…]`

The tree hit the node budget (default 2000). Narrow it:

- `format: "outline"` — compact indented text instead of JSON,
- lower `max_depth`,
- `read_element` a single element instead of the whole tree,
- raise `max_nodes` (up to 20000) only if you truly need more.

## A tap "succeeds" but nothing happens

- The element may be **scrolled out of view**; a tap outside the window's visible
  bounds is refused. `scroll` it into view first (negative `delta_y` scrolls
  down), then retry.
- Some controls have no `AXPress` and aren't selectable — the click falls back to
  posting coordinates. Verify with `assert_visible` / `get_ui_tree` after acting.

## `input_text` refuses the field

Password fields (`AXSecureTextField`) are refused by design — enter credentials
manually. For normal fields, `input_text` replaces the content by default; pass
`clear_first: false` to append.

## Letter/number keyboard shortcuts

`press_key` accepts named keys **and** single letters/digits with modifiers:
`key:"s", modifiers:["command"]` → ⌘S.

## My license won't activate

- Activation needs a network connection the first time and periodically after.
  Check that you pasted the full key (starts with `KURO1.`).
- An Individual license allows up to 3 devices. If you've hit the limit,
  deactivate an old device from **Settings → License** on that Mac first.
- Refunded licenses are revoked and won't activate.
