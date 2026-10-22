# Part 1: Your computer

## 1. Set up OpenRouter (you)

OpenRouter is a single account that gives access to many AI models, including
Claude, and bills per use. You'll give Claude Code and Jarvis separate keys, so
each one has its own limit and you can revoke one without the other.

1. Sign up at openrouter.ai.
2. Buy a small amount of credit, for example 30 $. OpenRouter bills in US
   dollars and adds a small fee to each purchase **(verify)**. Leave
   auto top-up off. That makes your balance a hard cap. When it runs out,
   everything stops instead of spending more.
3. Under Settings → Privacy **(verify)**: leave prompt logging off, and turn
   off providers that may train on your inputs. Jarvis will send your
   messages, voice recordings and notes through this account.
4. Under Settings → Provider preferences **(verify)**, put Anthropic first.
   OpenRouter only guarantees that Claude Code works with Anthropic as the
   provider.
5. Under Keys, create a key named `claude-code` with a credit limit of about
   20 $ **(verify)**. Don't copy it anywhere yet. Leave the page open for
   step 2.

You create Jarvis's key in step 9, through the wizard.

## 2. Set up Claude Code (you)

1. Install Claude Code: `curl -fsSL https://claude.ai/install.sh | bash`
2. Clone this repo with
   `git clone https://github.com/jthies73/jarvis.git ~/jarvis`. Always start
   Claude Code from inside it. The repo holds the guide, the wizard and the
   house rules.
3. Store the `claude-code` key in your Keychain. Run the following, then paste
   the key at the hidden prompt:

   ```sh
   security add-generic-password -a "$USER" -s openrouter-claude-code -w
   ```

   On Linux, use `secret-tool store --label=openrouter-claude-code service
   openrouter-claude-code` instead, and use
   `secret-tool lookup service openrouter-claude-code` as the `apiKeyHelper`
   below. Then close the OpenRouter page. The key isn't shown again, and you
   won't need it.
4. Create the `~/jarvis/.claude` folder and save this as
   `~/jarvis/.claude/settings.local.json`. It points Claude Code at OpenRouter
   for this folder only. Git ignores it:

   ```json
   {
     "apiKeyHelper": "security find-generic-password -s openrouter-claude-code -w",
     "model": "sonnet",
     "env": {
       "ANTHROPIC_BASE_URL": "https://openrouter.ai/api",
       "ANTHROPIC_API_KEY": "",
       "ANTHROPIC_DEFAULT_OPUS_MODEL": "~anthropic/claude-opus-latest",
       "ANTHROPIC_DEFAULT_SONNET_MODEL": "~anthropic/claude-sonnet-latest",
       "ANTHROPIC_DEFAULT_HAIKU_MODEL": "~anthropic/claude-haiku-latest"
     }
   }
   ```

   Claude Code runs `apiKeyHelper` to fetch the key itself, so the key never
   sits in a file or your shell environment **(verify)**. The empty
   `ANTHROPIC_API_KEY` stops Claude Code from using an Anthropic key left over
   in your shell.
5. Run `cd ~/jarvis && claude`. Don't log in with an Anthropic account. If you
   logged in to Claude Code with one before, run `/logout` and start `claude`
   again, because the old login causes "model not found" errors.
6. Install the wizard skill with
   `npx skills add mattpocock/skills --skill wizard` and pick Claude Code when
   asked **(verify)**.
7. If you don't have an SSH key yet, run `ssh-keygen -t ed25519`, keep the
   default path and set a passphrase.

The house rules (`AGENTS.md`, which `CLAUDE.md` points to) came with the clone.
Read `AGENTS.md` once yourself, since it tells the agent what it may and may
not do. A fresh session forgets the conversation but still reads the house
rules and `LOG.md`, so nothing important is lost.

Keep Claude Code in its default permission mode, where it asks before every
command. When it asks, pick "Yes", not the option that stops it asking about
that command again. Don't switch to auto-accept with Shift+Tab, and never
start it with `--dangerously-skip-permissions`.
Approving each command yourself is how you follow what the agent is doing.

Every message costs credit, and a long chat costs more per message because the
whole conversation is sent each time. `/cost` shows what the session has spent.
If the `claude-code` key reaches its limit, Claude Code stops with an error.
Raise the limit on OpenRouter's Keys page and carry on.

Done when `/status` shows the base URL `https://openrouter.ai/api` and the
API key coming from `apiKeyHelper` **(verify)**, a test message to Claude Code
appears under the `claude-code` key on OpenRouter's Activity page, `/skills`
lists `wizard` **(verify)**, and `~/.ssh/id_ed25519.pub` exists.
