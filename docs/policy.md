# App policy (`policy.json`)

kuroko hands an AI agent the ability to read and drive your apps in the
background. The policy is the guardrail that decides **which apps a tool call may
touch** and **whether state-changing tools are active at all**.

It lives at:

```
~/Library/Application Support/kuroko/policy.json
```

If the file is missing or malformed, safe defaults apply (denylist mode, the
built-in sensitive apps blocked, write tools armed). The policy is read fresh on
every tool call, so edits take effect immediately — no restart.

## Fields

| Field | Type | Default | Meaning |
|-------|------|---------|---------|
| `mode` | `"denylist"` \| `"allowlist"` | `"denylist"` | `denylist`: everything is allowed except blocked apps. `allowlist`: only apps in `allow` are permitted. |
| `allow` | `[string]` | `[]` | Bundle ids explicitly permitted (used in `allowlist` mode). |
| `deny` | `[string]` | `[]` | Extra bundle ids to block, merged with the built-in denylist. |
| `armWriteTools` | `bool` | `true` | When `false`, all state-changing tools are inert (a kill switch). |

**Write tools** (gated by `armWriteTools`): `tap`, `double_tap`, `input_text`,
`press_key`, `scroll`, `launch_app`, `stop_app`. Read tools (`get_ui_tree`,
`screenshot`, `read_element`, `assert_visible`, `list_apps`,
`check_permissions`) are never gated by arming, but are still subject to the
allow/deny rules.

## Built-in denylist (always blocked)

These high-value apps are blocked even in `denylist` mode with an empty `deny`,
so a prompt-injected agent can't type into a shell or read a vault:

- `com.apple.Terminal`, `com.googlecode.iterm2`
- `com.apple.keychainaccess`
- `com.1password.1password`, `com.1password.1password-launcher`, `com.agilebits.onepassword7`
- `com.apple.systempreferences`, `com.apple.SystemSettings`

To let an agent drive one of these anyway you must switch to `allowlist` mode and
list it explicitly — a deliberate, visible choice.

## Examples

**Default (no file needed)** — drive anything except the sensitive built-ins:

```json
{ "mode": "denylist", "armWriteTools": true }
```

**Strict allowlist** — only your app under test can be touched at all:

```json
{
  "mode": "allowlist",
  "allow": ["com.klimakov.gittool"],
  "armWriteTools": true
}
```

**Read-only session** — the agent can look but not act:

```json
{ "mode": "denylist", "armWriteTools": false }
```

**Block extra apps** on top of the built-ins (e.g. a browser with a live
banking session):

```json
{ "mode": "denylist", "deny": ["com.apple.Safari", "com.google.Chrome"] }
```

## What a blocked call looks like

The tool returns an error result:

```
Blocked by policy: 'com.apple.Terminal' is on the denylist (sensitive app). …
```

Agents can read the live policy via the `policy://current` MCP resource to see
what's allowed before making a call. See [troubleshooting.md](troubleshooting.md)
if calls are blocked or failing unexpectedly.
