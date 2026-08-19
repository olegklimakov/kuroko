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

Open Kuroko's **Connect** tab. It lists every agent host it finds on this Mac —
Claude Code, Claude Desktop, Codex & ChatGPT Desktop, Cursor, Windsurf, VS Code —
each with its live status and one button that adds Kuroko's MCP entry for you (and
removes it again later). Hosts that read skills — Claude Code and Codex — also get
the `kuroko` skill installed alongside the entry, so the agent knows how to use
the tools without hand-holding.

Kuroko edits these config files rather than replacing them: where a host ships its
own CLI the change goes through it, and everything else is written atomically,
keeping the file's permissions and leaving a `.kuroko-backup` of the original the
first time it is touched.

### By hand

The bundled server binary is at
`/Applications/Kuroko.app/Contents/Helpers/kuroko`.

**Claude Code — plugin.** One install gets you the MCP server config *and* the
skill:

```
/plugin marketplace add olegklimakov/kuroko
/plugin install kuroko@kuroko
```

**Claude Code — MCP server only:**

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
**Connect** tab's manual section shows the same JSON with your resolved path.

The server also exposes the skill's guidance as a `guide://usage` MCP resource,
which any host can pull on demand.

## 4. Set a policy (recommended)

By default sensitive apps (Terminal, Keychain, 1Password, System Settings) are
blocked and all other apps are drivable. To restrict the agent to just your app
under test, create `~/Library/Application Support/kuroko/policy.json`:

```json
{ "mode": "allowlist", "allow": ["com.example.yourapp"], "armWriteTools": true }
```

Full reference: [policy.md](policy.md).

## 5. Try it

Ask your agent something concrete against your app, then watch it appear in the
app's **Activity Log** (or `~/Library/Application Support/kuroko/actions.jsonl`).

## 6. Buy a license

The trial lasts 14 days. To keep using Kuroko, buy a license at
[klimakov.me/projects/kuroko/buy](https://klimakov.me/projects/kuroko/buy) and
activate it in **Settings → License**.
