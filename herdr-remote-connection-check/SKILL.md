---
name: herdr-remote-connection-check
description: >-
  Use when herdr cannot reach a remote machine (timeouts, 'server status probe
  timed out', ssh connect timed out) or before reading a remote agent pane, to
  diagnose and fix the box-side Tailscale/SSH path.
---
# herdr remote connection check

Goal: find out in under a minute why `herdr --machine <machine>` fails, fix box-side causes, and only then ask the user to act on the remote machine.

## Steps
1. Try herdr first: `timeout 60 herdr --machine <machine> agent list`. If it returns agents, you are done.
2. Look up the machine's Tailscale IP and SSH port (`herdr machine list`, `tailscale status`).
3. Check the box's own Tailscale health: `tailscale status` (look at the box's own row and the `# Health check` lines). Signs of a box-side fault: the box shows as `offline`, or the health check says it "hasn't received a network map from the coordination server".
4. Test the network and the port:
   - `tailscale ping <ip>` (reachability; a DERP relay is fine)
   - `timeout 15 bash -c 'echo > /dev/tcp/<ip>/<port>' && echo OPEN || echo CLOSED`
5. If the box is offline or missing its network map, restart tailscaled with the same arguments it was started with (read them from `pgrep -a tailscaled`). Kill that PID, relaunch it in the background with `sudo nohup tailscaled <same args> &`, wait about 10 s, then repeat step 4. Do not run `tailscale logout` or `tailscale down`, which may need a fresh sign-in.
6. Once the port is OPEN, rerun step 1 and continue with `herdr --machine <machine> agent read <pane>`.
7. Only if the box's Tailscale is healthy, ping works and the port is still CLOSED, suspect the remote machine (for example WSL stopped, sshd not running, or a firewall/port-forward). Ask the user to check `sudo service ssh status` there, and start it if needed. Mention that if the user's own laptop can connect, the fault is more likely on the box side, so recheck step 3.

## Notes
- Never print credentials or keys while debugging.
- Report the root cause in one or two plain sentences, not the raw command output.
