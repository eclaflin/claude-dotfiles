# eza — Modern ls Replacement

## Mental model
eza is `ls` with useful defaults: color, icons, git status, human-readable sizes, and tree view — all without memorizing cryptic flags.

## Orient talking points
- The old way: `ls -lahF --color=auto` every time, or a long alias chain
- eza gives you sane defaults and new capabilities (`--git`, `--tree`, icons)
- It's a drop-in replacement — same muscle memory, better output

## Phase 2: Demo sequence

### 1. Basic listing vs ls
```bash
ls
eza
```
Point out: color-coded by type, icons (if nerd fonts), cleaner output.

### 2. Long format
```bash
eza --long
# or: eza -l
```
Compare to `ls -l`. Point out: human-readable sizes by default, cleaner date format.

### 3. Git integration
```bash
eza --long --git
```
Run this inside a git repo. The `Git` column shows file status (unmodified, modified, new, ignored). This is unique to eza.

### 4. Tree view
```bash
eza --tree --level=2
```
Built-in tree with the same color/icon formatting. No need for a separate `tree` command.

### 5. Hidden files + sort
```bash
eza --long --all --sort=modified
```
Shows dotfiles, sorted by modification time (most recent last). Flip with `--sort=modified --reverse` for newest first.

## Phase 3: Guided Practice

1. "Run `eza --long` in your home directory. What's different from regular `ls -l`?"
2. "Try `eza --tree --level=2` in `~/.config` (or any directory with subdirs). How deep does it go?"
3. "Now try `eza --long --all --sort=size --reverse` somewhere with mixed file sizes. What's at the top?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-eza-$(date +%s)
mkdir -p $SANDBOX
cd $SANDBOX

# Mix of file types, sizes, and a git repo
git init -q
mkdir -p src tests docs .hidden-dir
dd if=/dev/zero of=large-file.bin bs=1M count=2 2>/dev/null
echo "hello" > small.txt
echo "# Notes" > docs/notes.md
touch src/main.py src/helpers.py tests/test_main.py
echo "# ignore" > .gitignore
echo "*.bin" >> .gitignore
git add src/ docs/ .gitignore small.txt tests/
git commit -q -m "initial commit"
echo "new change" >> src/main.py
touch src/new-feature.py
```

### Challenge
> "The sandbox at `$SANDBOX` has a mix of tracked, modified, new, and ignored files, plus a hidden directory and a large binary. Your challenge: use a single `eza` command that shows you (1) all files including hidden, (2) their sizes in human-readable form, (3) their git status, and (4) sorted by size largest-first. What command did you use?"

**Solution:**
```bash
eza --long --all --git --sort=size --reverse
```
Bonus: add `--icons` if nerd fonts are installed.
