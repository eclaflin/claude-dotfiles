# tmux — Terminal Multiplexer

## Mental model
tmux lets you have multiple terminals inside one terminal window, and keeps them alive when you disconnect. Think of it as a window manager that lives inside your terminal, plus the ability to "save" a session and come back to it.

## Orient talking points
- The old way: multiple terminal tabs/windows, all dying if your SSH connection drops
- tmux gives you: multiple panes in one window, multiple windows in one session, sessions that persist indefinitely
- The two things that matter most: (1) pane splitting, (2) session detach/attach

## Phase 2: Demo sequence

Note: tmux is interactive — you can't fully demo it via Bash tool. Walk through concepts with explanations and have the user try in their own terminal.

### 1. Session basics
```bash
# Start a new named session
tmux new-session -s demo
# (explain: the user is now inside tmux — the status bar at bottom is the indicator)
```
Point out the green status bar. Explain: this is now a session named "demo".

### 2. Key prefix
All tmux commands start with `Ctrl+b` (the prefix), then another key. This is how tmux distinguishes "talk to tmux" from "talk to the shell".

Key bindings to cover:
- `Ctrl+b ?` — list all bindings (the help screen)
- `Ctrl+b d` — detach from session (session keeps running)
- `Ctrl+b $` — rename session

### 3. Pane splitting
```
Ctrl+b %   — split vertically (left/right)
Ctrl+b "   — split horizontally (top/bottom)
Ctrl+b arrow — move between panes
Ctrl+b z   — zoom a pane (toggle fullscreen)
```
Demonstrate: split, run something in each pane, zoom in/out.

### 4. Windows (tabs)
```
Ctrl+b c   — create a new window
Ctrl+b n   — next window
Ctrl+b p   — previous window
Ctrl+b w   — list all windows (pick one)
Ctrl+b ,   — rename window
```
Mental model: windows are like browser tabs. Panes are splits within a tab.

### 5. Detach and reattach (the killer feature)
```bash
# Inside tmux:
# Ctrl+b d   (detach — session keeps running)

# From outside:
tmux ls                    # list sessions
tmux attach -t demo        # reattach
tmux attach                # attach to most recent
```
This is why tmux matters for remote work: start a long-running process, detach, SSH disconnect, reconnect later — it's still running.

## Phase 3: Guided Practice

1. "Start a new tmux session with `tmux new-session -s practice`. What do you see at the bottom of your terminal?"
2. "Press `Ctrl+b %` to split vertically. Then press `Ctrl+b "` to split the right pane horizontally. Now use `Ctrl+b arrow` to move between panes. Run a different command in each pane."
3. "Press `Ctrl+b d` to detach. You should be back at your normal terminal. Now run `tmux ls`. Then reattach with `tmux attach -t practice`."

## Phase 4: Sandbox Setup

```bash
# The sandbox is a tmux session itself
tmux new-session -s tutor-challenge -d
tmux send-keys -t tutor-challenge "mkdir -p /tmp/cli-tutor-tmux && cd /tmp/cli-tutor-tmux" Enter
tmux send-keys -t tutor-challenge "echo 'Sandbox ready. Type: tmux attach -t tutor-challenge'" Enter
```

### Challenge
> "Attach to the `tutor-challenge` tmux session I created. Inside it, set up this layout: **a top pane running `htop` (or `top`), a bottom-left pane with a shell in `/tmp/cli-tutor-tmux`, and a bottom-right pane watching a log file with `tail -f /var/log/syslog` (or any log)**. Then rename the window to 'monitor'. Detach when done. Here's the sequence to figure out: which split commands do you need, in which order?"

**Solution:**
```
# Attach
tmux attach -t tutor-challenge

# Split to get top/bottom
Ctrl+b "

# Go to bottom pane, split left/right
Ctrl+b %

# Go to top pane, run htop
Ctrl+b (up arrow)
htop

# Go to bottom-left, it's already a shell
Ctrl+b (left arrow)
# already in /tmp/cli-tutor-tmux from setup

# Go to bottom-right, tail log
Ctrl+b (right arrow)
tail -f /var/log/syslog

# Rename window
Ctrl+b ,
monitor

# Detach
Ctrl+b d
```
