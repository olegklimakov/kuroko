# Install & connect

Kuroko is an MCP server you hand to an AI agent so it can see and drive native
macOS apps in the background. This takes you from a download to a working agent
connection.

> Requires **macOS 14 or later** (background window capture needs it).

## 1. Download & install

1. Download the latest `Kuroko.dmg` from
   [Releases](https://github.com/olegklimakov/kuroko/releases/latest).
2. Open the DMG and drag **Kuroko** into **Applications**.
3. Launch **Kuroko** — a 14-day trial starts automatically.

Kuroko is signed with a Developer ID and notarized, so Gatekeeper opens it without
warnings. It doesn't ship on the Mac App Store because the sandbox forbids driving
other apps.

## 2. Grant permissions — to the right process

macOS grants Accessibility and Screen Recording **per process**, to the exact
binary that asks. The agent runs the Kuroko server as a child of your **terminal /
agent host** (Terminal, iTerm, VS Code, Claude Desktop, …), so **that app** needs
the grant — **not** `Kuroko.app`.

- **Accessibility** (required): System Settings → Privacy & Security →
  Accessibility → enable your terminal / host.
- **Screen Recording** (only for the `screenshot` tool): same, under Screen
  Recording.

Kuroko's dashboard can be fully green while agent calls still fail, because the
dashboard reflects the GUI's own process. Ask the agent to call
`check_permissions` to see the **server process's** real status.

## 3. Connect it to your agent

The bundled server binary is at
`/Applications/Kuroko.app/Contents/Helpers/kuroko`.

**Claude Code:**

```bash
claude mcp add kuroko -- "/Applications/Kuroko.app/Contents/Helpers/kuroko" mcp
```

**Claude Desktop** (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "kuroko": {
      "command": "/Applications/Kuroko.app/Contents/Helpers/kuroko",
      "args": ["mcp"]
    }
  }
}
```

Any MCP-capable host works — point it at the binary with the `mcp` subcommand. The
app's **Connect** tab shows the exact command and your resolved path.

## 4. Set a policy (recommended)

By default sensitive apps (Terminal, Keychain, 1Password, System Settings) are
blocked and all other apps are drivable. To restrict the agent to just your app
under test, create `~/Library/Application Support/kuroko/policy.json`:

```json
{ "mode": "allowlist", "allow": ["com.example.yourapp"], "armWriteTools": true }
```

Full reference: [policy.md](policy.md).

## 5. (Optional) Install the Claude Code skill

If a `skills/kuroko/` skill ships with a release, install it into a Claude Code
skills directory so the agent uses the tools well without hand-holding:

```bash
cp -R skills/kuroko ~/.claude/skills/kuroko
```

The server also exposes the same guidance as a `guide://usage` MCP resource that
any host can pull on demand.

## 6. Try it

Ask your agent something concrete against your app, then watch it appear in the
app's **Activity Log** (or `~/Library/Application Support/kuroko/actions.jsonl`).

## 7. Buy a license

The trial lasts 14 days. To keep using Kuroko, buy a license at
[klimakov.me/projects/kuroko/buy](https://klimakov.me/projects/kuroko/buy) and
activate it in **Settings → License**.
