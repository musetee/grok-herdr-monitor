# herdr-agent-monitor

A Grok Bot skill that gives a short, read-only status report of every AI agent running on one or more remote [herdr](https://github.com/) machines: what each agent is working on, whether it is idle, working, or blocked, and anything it is waiting on from you. It never types into panes or approves prompts.

## Install

Send this repo's URL to your Grok Bot and ask it to save `herdr-agent-monitor/SKILL.md` as a skill. Then ask it "what are my herdr agents doing?"

## Requirements

- `herdr` installed on the bot's computer, with your remote machines registered (`herdr machine list`).
- Network/SSH access from the bot's computer to those machines (for example over Tailscale).
- Optional: a `herdr-remote-connection-check` skill for diagnosing connection failures; without it, unreachable machines are simply reported as unreachable.

Nothing is hardcoded: machine names, panes, and addresses are discovered at runtime.
