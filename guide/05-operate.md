# Part 5: Running and growing Jarvis

## When something breaks

Ask the agent. It explains what it finds, as the house rules require.

- **The bot is silent, or voice isn't understood.** On OpenRouter, check your
  balance and whether the `jarvis` or `jarvis-voice` key has reached its
  limit. With auto top-up off, Jarvis stops when either runs out. Otherwise,
  have the agent read the Hermes service log with `journalctl`. For voice,
  it looks for transcription errors.
- **Claude Code stops with a credit or payment error.** The `claude-code` key
  has reached its limit, or the balance is empty. Raise the limit or add
  credit on OpenRouter, then carry on.
- **Claude Code says "model not found".** Run `/status` and check that the
  base URL is OpenRouter. If you were ever logged in with an Anthropic
  account, run `/logout` and restart `claude`.
- **Random crashes or out of memory.** Have the agent run
  `vcgencmd get_throttled` to check the power supply, then `free -m` and the
  kernel log for OOM kills.
- **Jarvis forgets things or repeats questions.** Ask it what's in its memory
  and in the workspace. Have the agent check memory usage and the curator
  with `hermes curator status`. The agent can restore archived skills.
- **The SD card died.** Flash a new card (step 3), run the wizard's `connect`
  stage, and ask the agent to restore from `~/jarvis/backup/` using the steps
  in LOG.md. Then run the `secrets` and `voice` stages again.
- **Jarvis learned something wrong or changed itself oddly.** Have the agent
  show the latest commits in Hermes's git repository and undo the bad one.
- **A secret leaked.** Revoke it where it came from: BotFather `/revoke`, or
  OpenRouter's Keys page for `jarvis` and `jarvis-voice`. Then run the
  matching wizard stage again. For the `claude-code` key, create a new
  one and repeat step 2.3 with
  `security add-generic-password -U -a "$USER" -s openrouter-claude-code -w`.

## Adding features

Jarvis is meant to grow. Before you add a feature, ask three questions:

1. Does it bring in content from other people, like web pages, emails,
   shared notes or network scans?
2. Does it give Jarvis access to something valuable, like an account, your
   files or a NAS?
3. Does it let Jarvis act outside the Pi, like sending, deleting or switching
   things?

One "yes" is fine. When a feature combines all three, add a guard: read-only
access, a separate system user that holds the credentials and gives Jarvis
only a fixed set of tools (an MCP server), or an approval step. For example,
mount a NAS read-only at first. Google Keep or email would answer "yes" to
all three, so they get their own user and MCP server. Run the backup after
every new feature.

If keyword search over your notes stops being enough, ask Claude Code about a
vector search. It needs an extra component and is heavy for a Pi 3, so don't
add it up front.

### Connecting an MCP server

Do the connecting from Claude Code over SSH, not from Telegram. Connecting an
MCP server changes Hermes's config, often installs software, and usually
needs a secret or a browser login. Claude Code can test the result and fix
it. If a bad config stops Hermes from starting, you can't repair it through
the bot. Telegram is for using the tools afterwards.

You will still ask for new integrations from your phone. So Jarvis doesn't
try them. It writes a request down, and Claude Code picks it up later without
you explaining anything again. Claude Code already knows what to do with
the entries, because `AGENTS.md` has a rule for it. Set up the Jarvis side
once:

> On jarvis, create a Hermes skill called `request-mcp`
> **(verify the skill format in the Hermes docs)**. When I ask on Telegram to
> connect a service, tool or MCP server, Jarvis must not install or configure
> anything. It appends an entry to
> `/home/hermes/workspace/requests/mcp.md` with the date, what I want to do
> with it, which service or MCP server I mean (a link if I gave one), what
> Jarvis knows about it, and `status: open`. It never writes a token, key or
> password into the entry. If I send a secret anyway, it tells me to delete
> the message and doesn't store it. Then it replies in two lines: the
> request is saved, and I should ask Claude Code on my computer to continue.

Done when you send "Connect Google Keep" from Telegram, Jarvis only answers
that the request is saved, and a new Claude Code session starts by telling
you about it. Ask whether the new MCP server passes the three questions above
before you accept the plan.
