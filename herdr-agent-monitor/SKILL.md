---
name: herdr-agent-monitor
description: >-
  Use when asked what the AI agents on a remote herdr machine are doing: lists
  every agent there, reads each one's pane read-only, and reports a short status
  and current task per agent.
---
# herdr agent monitor

Goal: a short, read-only status report of every agent on one or more remote herdr machines. Never send keys, text, or prompts to any pane.

## Steps
1. Discover machines at runtime: `herdr machine list` (and `herdr machine status` to see which are reachable). If the user named a machine, use that label; otherwise check every enabled one.
2. For each `<machine>`, list agents: `timeout 60 herdr --machine <machine> agent list`. If this times out or fails, run the herdr-remote-connection-check skill first, then retry once. If it still fails, report the machine as unreachable and move on.
3. From the list, note for each agent its `<pane>` id, agent type, status (idle, working, blocked, ...), workspace or terminal title, and cwd.
4. Read each agent's recent output, read-only: `timeout 30 herdr --machine <machine> agent read <pane> --source recent --lines 80`.
5. For each agent, work out from that output:
   - what task it is on (the latest request, plan or tool step),
   - whether it is working, idle at an empty input box, or blocked,
   - if blocked: the exact question or permission prompt and its options (quote the command or question; never choose an option),
   - anything it says it is waiting for from the user (a PR to merge, a decision, a manual test).
6. Report one short block per machine, one line or two per agent: title or pane, status, current task, and any ask for the user. Put blocked agents and open asks first. Keep the report in plain words, not raw terminal output.

## Notes
- Do not hardcode machine names, IPs, ports or pane ids; always discover them with the commands above.
- Never approve, deny or type into a prompt; only report it.
- Never print credentials or keys that appear in pane output.
