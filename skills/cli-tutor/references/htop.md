# htop — Interactive Process Viewer

## Mental model
htop is `top` with a real UI. You can see all processes, sort them, filter by name, kill them, and understand what's using your CPU/memory — all interactively, without memorizing arcane commands.

## Orient talking points
- The old way: `top` — hard to read, hard to interact with
- htop shows a proper list with bars, lets you sort by any column, filter, and act on processes
- The two main uses: (1) "what's eating my CPU/RAM right now?" (2) "how do I kill this stuck process?"

## Phase 2: Demo sequence

Note: htop is interactive — you can't fully demo it via Bash. Walk through it with the user following along.

### 1. Opening and orientation
```bash
htop
```
Point out:
- **Top bars**: CPU cores (each bar), Memory, Swap
- **Process list**: PID, USER, CPU%, MEM%, TIME+, Command
- **Bottom bar**: function key shortcuts

### 2. Sorting
```
F6 (or click column header) — sort by column
P — sort by CPU
M — sort by memory
T — sort by time
```
Sort by CPU to find what's pegging a core. Sort by memory to find the RAM hog.

### 3. Filtering (search)
```
F4 — filter by process name
/  — also works for filtering
```
Type a process name to narrow the list. Clear with Backspace.

### 4. Killing a process
```
F9 — kill selected process
k  — also works
```
Select a process, press F9 (or k), choose signal. SIGTERM (15) is graceful. SIGKILL (9) is forceful — use when SIGTERM doesn't work.

### 5. Tree view
```
F5 — toggle tree view
```
Shows parent/child relationships. Useful for seeing which shell spawned which process.

### 6. Useful toggles
```
H — show/hide user threads
K — show/hide kernel threads  
t — toggle tree view (same as F5)
u — filter by user
```

## Phase 3: Guided Practice

1. "Open `htop`. Without doing anything, what's the process using the most CPU? How much memory is your system using total?"
2. "Press `M` to sort by memory. What's the top memory consumer? Is it what you expected?"
3. "Press `F4` and type your shell name (e.g., `zsh`). How many instances are running? Press Escape to clear the filter."
4. "Press `F5` to toggle tree view. Find your `htop` process in the tree — what's it running under?"

## Phase 4: Sandbox Setup

```bash
# Start some background processes to give htop something to show
python3 -c "
import time, math
# burn a little CPU
while True:
    sum(math.sqrt(i) for i in range(10000))
    time.sleep(0.01)
" &
CPU_HEAVY_PID=$!

python3 -c "
# use some memory
data = [bytearray(1024*1024) for _ in range(50)]  # 50MB
import time
time.sleep(60)
" &
MEM_HEAVY_PID=$!

echo "Started processes: CPU-heavy PID=$CPU_HEAVY_PID, Memory-heavy PID=$MEM_HEAVY_PID"
echo "Open htop, find and kill them both. Then come back here."
```

### Challenge
> "I've started two background processes — one burning CPU, one holding 50MB of memory. Both are python3 processes. Your challenge: open htop, find both of them (hint: filter with F4 for 'python3'), identify which is which by their resource usage, and kill them both gracefully with SIGTERM. Confirm they're gone by checking the process list. What's the PID of each? (I printed them above.)"

**Cleanup:**
```bash
# If they're still running:
kill $CPU_HEAVY_PID $MEM_HEAVY_PID 2>/dev/null
```
