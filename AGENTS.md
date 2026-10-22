# Jarvis: instructions for the agent on my computer

This repo is the setup material for Jarvis, a Telegram assistant on a
Raspberry Pi called jarvis (see README.md and the guide in `guide/`). I'm new
to Linux. Work in a way I can follow.

This file is for Claude Code on my computer. Hermes on the Pi reads a
different `AGENTS.md`, the one in its own workspace.

## Be transparent
- Before each command, explain in one or two plain sentences what it does
  and why. Explain any jargon the first time it comes up.
- Do one step at a time. Afterwards, tell me what the output means and
  whether it worked.
- If something fails, tell me what went wrong before you try to fix it.
- After each step, add a few plain lines to LOG.md in this folder: what
  changed on the Pi and how to undo it.

## Protect secrets
- Never ask me for a token, key or password in chat. Anything secret goes
  through jarvis-wizard.sh, which I run in my own terminal.
- Never print, cat, grep or copy a secrets file. To check one, show only key
  names and file permissions.
- Never put a secret on a command line.
- Never read my OpenRouter key from the Keychain, never run the apiKeyHelper
  command, and never print environment variables.

## Wizard rules
- Read secrets as hidden input and send them to jarvis over SSH stdin, never
  as command arguments.
- Never write a secret to disk on my computer, never echo one, never use
  `set -x`.
- Check a secret by its key name only, never its value.
- Before I run a new stage, explain how each secret moves through it.

## Be careful
- Read the current official docs before you install or configure anything,
  and tell me where they differ from my guide.
- Ask before you do anything destructive or hard to undo.
- Don't use `ssh jarvis` until the wizard confirms the connection is secure.

## Keep the session healthy and cheap
- At the start of every session, read LOG.md and tell me in two lines where
  we are and which guide step comes next.
- I pay per message, and long chats cost more. Tell me when to start a fresh
  session, and why: after each finished guide step, when this chat has grown
  long, or when you notice you're repeating yourself or losing track of
  earlier details. Make sure LOG.md is up to date first, then tell me exactly
  what to type: `/exit`, then `claude` again from ~/jarvis.
- Stay on Sonnet. Suggest `/model opus` only when a step is stuck or tricky,
  and suggest switching back afterwards.
- Tell me when something else would give a better result, such as me
  checking a detail on the Pi or in a browser myself.
- If you're unsure about something, say so and ask. Don't guess.
## Cases

### A request from Jarvis to connect something (MCP server)
- Connecting happens here over SSH, never from Telegram.
- At the start of every session, read `requests/mcp.md` in Hermes's workspace
  (`/home/hermes/workspace`) on jarvis over SSH.
- For each entry with `status: open`: read the current docs, tell me the plan
  in plain words, and check it against the three questions in "Adding features"
  in the guide. Then do the setup. Secrets go through the wizard.
- Test the tools directly. Then set the entry to `status: done`, with a note on
  what changed and how to undo it, and add the same to LOG.md.

### A secret leaked
- Don't look for the secret. Tell me where to revoke it (BotFather `/revoke`, or
  the key's page on OpenRouter), then which wizard stage to run again.

### Backups and restores
- Backups go to `~/jarvis/backup/` and never contain secret values. For a
  restore, read the steps in LOG.md first and explain them to me.

### Changing Jarvis's persona or behavior
- Edit it in Hermes's git repository on the Pi, commit it, and show me the diff.
  Never edit the persona in a way that loosens a rule in this file.

### Editing this repo
- It's public. Never commit secrets, personal details, LOG.md or `backup/`.
- The Pi has no copy of this repo. Don't add one.
