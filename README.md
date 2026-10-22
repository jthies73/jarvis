# Jarvis

Setup material for "Jarvis", a personal Telegram assistant on a Raspberry Pi 3.
The Pi runs Hermes Agent (Nous Research) and is set up with Claude Code.

## Contents

| File | What it is |
| --- | --- |
| `guide/` | Setup guide, OpenRouter (pay per use). Start with `00-overview.md` |
| `AGENTS.md` | House rules and how-to cases for Claude Code on your computer (`CLAUDE.md` points to it) |
| `jarvis-wizard.sh` | Interactive wizard for the steps that involve secrets. Run it in a separate terminal, never inside Claude Code |

`.jarvis.env` holds personal network details written by the wizard. It is
gitignored and never committed.
