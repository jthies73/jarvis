# Jarvis on a Raspberry Pi 3 (OpenRouter edition)

This guide sets up a Raspberry Pi 3 as "Jarvis", a laid-back Telegram
assistant. It understands voice messages and keeps your lists, notes and
projects in its own memory and workspace. It answers only your Telegram
account.

The Pi runs Hermes Agent by Nous Research. You set it up with Claude Code, an AI
assistant that runs in a terminal on your computer. There's no subscription.
Claude Code and Jarvis both pay per use from prepaid OpenRouter credit, and
each has its own spending limit.

**(verify)** marks details nobody has checked against current docs yet.
The agent reads the current docs before each step anyway.

## Where to find what

| File | Steps |
| --- | --- |
| `01-computer.md` | 1 OpenRouter, 2 Claude Code |
| `02-pi.md` | 3 flash, 4 connect, 5 lock down, 6 memory, 7 remote, 8 Hermes |
| `03-jarvis.md` | 9 secrets, 10 Telegram, 11 voice |
| `04-structure.md` | 12 topics and workspace, 13 backup, 14 test |
| `05-operate.md` | When something breaks, adding features |

## How this works

The work is split three ways:

- **You** do anything that needs your hands, your eyes or a browser.
- **The agent** is Claude Code on your computer. Once the connection to the Pi
  is secure, it works on the Pi over SSH. It explains each command before
  running it and asks for your approval.
- **The wizard** is a bash script the agent writes with the `wizard` skill.
  It handles everything that involves a secret: connecting to the Pi, logins,
  tokens and keys. You run it yourself in a **second terminal window**. It
  explains each click, reads secrets as hidden input, sends them straight to
  the Pi and checks that they arrived.

Secrets never pass through the agent. So never paste a token, key or password
into the Claude Code chat. And never run the wizard from inside Claude Code,
not even with `!`, because anything a command prints there goes to Claude.

There's one exception. Claude Code needs its own OpenRouter key to work at all,
so that key lives on the computer the agent runs on. You store it in your
system's keychain, where Claude Code fetches it itself. The house rules forbid
the agent from reading it, and its spending limit caps the damage if it leaks.

The agent writes the wizard, so a bug in it could leak a secret. Before you run
a new wizard stage, read it or ask the agent to explain how each secret moves
through it. The same goes for the wizard skill itself, which comes from a third
party.

Once it's running, Jarvis is an independent agent too. It has a shell,
browses the web and teaches itself new skills. It also reads content from
other people, such as web pages and messages you forward, and those can
contain instructions aimed at Jarvis. So the setup lets Jarvis do a lot, and
limits what can go wrong:

- Jarvis runs as its own user, away from your files and without full admin
  rights (step 8).
- Git records every change Jarvis makes to itself or its workspace, and you
  can undo any of them (steps 10 and 12).
- Backups and spending limits cover the rest (steps 1, 9, 11 and 13).

## What you need

- a Raspberry Pi 3 (B or B+), the official 5 V / 2.5 A power supply, a
  microSD card of 16 GB or more (A1 rated) and a card reader
- an Ethernet cable (recommended; Wi-Fi works too)
- Telegram on your phone
- an OpenRouter account with prepaid credit. It pays for Claude Code during
  setup and for Jarvis afterwards (step 1).
- access to your home router's admin page
- optional: Tailscale, to reach the Pi from outside your home
- on your computer (Linux or macOS): Node.js 20 or newer (to install the wizard
  skill), and Raspberry Pi Imager
