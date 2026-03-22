# Discord Guild Sessions

Connect Claude Code to a dedicated Discord channel in your server. Based on the [official Discord plugin](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord) with added **guild session channel** management.

When `DISCORD_GUILD_ID` is set, the bot auto-creates (or reuses) a text channel in your server and scopes all communication to it. Messages from other channels and DMs are ignored. Each Claude Code session gets its own channel.

## What's different from the official Discord plugin?

- **Session channels** — auto-creates a dedicated text channel per session in your Discord server
- **Channel name suggestions** — suggests names based on your project directory
- **Session persistence** — reuses the same channel across restarts via a persistent session ID
- **`setup_session_channel` tool** — lets Claude interactively create channels on the user's behalf

Everything else (access control, pairing, DMs, reactions, attachments) works the same as the official plugin.

## Prerequisites

- [Bun](https://bun.sh) — install with `curl -fsSL https://bun.sh/install | bash`

## Quick Setup

> **Note:** If you have the official `discord` plugin installed, uninstall it first to avoid conflicts:
> ```
> /plugin uninstall discord@claude-plugins-official
> ```

**1. Create a Discord bot** at the [Developer Portal](https://discord.com/developers/applications). Enable **Message Content Intent** under Bot settings.

**2. Invite the bot** to your server. Under OAuth2 > URL Generator, select the `bot` scope with these permissions:

- Manage Channels *(required for session channel creation)*
- View Channels
- Send Messages
- Send Messages in Threads
- Read Message History
- Attach Files
- Add Reactions

**3. Install the plugin.**

```
/plugin install discord-guild-sessions
```

**4. Configure the bot token.**

```
/discord-guild-sessions:configure MTIz...
```

**5. Set your guild ID.**

Add to `~/.claude/channels/discord/.env`:

```
DISCORD_GUILD_ID=123456789012345678
```

To get the guild ID: enable Developer Mode in Discord (User Settings > Advanced), then right-click your server > Copy Server ID.

**6. Launch with the channel flag.**

```sh
claude --channels plugin:discord-guild-sessions
```

On first launch, Claude will ask you to pick a channel name. It suggests names based on your project directory (e.g., `claude-my-project`, `my-project`). You can also type a custom name.

**7. (Optional) Pre-set a channel name.**

Skip the interactive prompt by setting a name in `.env`:

```
DISCORD_SESSION_CHANNEL_NAME=my-claude-bot
```

## How session channels work

1. On gateway connect, the bot checks for `DISCORD_GUILD_ID`
2. If `DISCORD_SESSION_CHANNEL_NAME` is set, it creates/reuses that channel immediately
3. Otherwise, it prompts Claude to ask the user to pick a name via `setup_session_channel`
4. The session channel is auto-registered in access groups (`requireMention: false`)
5. All messages outside the session channel are dropped

Session IDs persist in `~/.claude/channels/discord/session_id` and survive restarts.

## Access control

See the official plugin's [ACCESS.md](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins/discord/ACCESS.md) — the same access model applies here.

## Tools

| Tool | Purpose |
| --- | --- |
| `reply` | Send to channel with optional threading and file attachments |
| `react` | Add emoji reactions |
| `edit_message` | Edit previously sent messages |
| `setup_session_channel` | Create or reuse a session channel in the guild |
| `fetch_messages` | Pull recent channel history |
| `download_attachment` | Download attachments to local inbox |

## License

Apache-2.0
