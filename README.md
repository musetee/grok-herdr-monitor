# herdr-agent-monitor

A Grok Bot skill that gives a short, read-only status report of every AI agent running on one or more remote [herdr] machines: what each agent is working on, whether it is idle, working, or blocked, and anything it is waiting on from you. It never types into panes or approves prompts.

## Install

Send this repo's URL to your Grok Bot and ask it to save both `herdr-agent-monitor/SKILL.md` and `herdr-remote-connection-check/SKILL.md` as skills. Then ask it "what are my herdr agents doing?"

## Requirements

- `herdr` installed on the bot's computer, with your remote machines registered (`herdr machine list`).
- Network/SSH access from the bot's computer to those machines (for example over Tailscale).
- `herdr-remote-connection-check` (included in this repo) for diagnosing connection failures; without it, unreachable machines are simply reported as unreachable.

Nothing is hardcoded: machine names, panes, and addresses are discovered at runtime.

## herdr-remote-connection-check

A companion skill for when herdr can't reach a remote machine (timeouts, "server status probe timed out", SSH connect timeouts). It checks the bot computer's Tailscale health, pings the remote machine and tests its SSH port, restarts the local Tailscale daemon with its existing settings if needed (never logs out), and only then points at the remote SSH server. The monitor skill calls it automatically on connection failures.
