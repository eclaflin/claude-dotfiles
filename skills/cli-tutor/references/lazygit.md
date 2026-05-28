# lazygit — Git TUI

## Mental model
lazygit is a full git interface inside your terminal. Everything you'd do with `git add -p`, `git log --graph`, `git rebase -i` is one or two keypresses — and you can see what's happening without memorizing flags.

## Orient talking points
- The old way: `git add`, `git status`, `git diff`, `git commit`, `git log` — separate commands, no overview
- lazygit shows you the whole picture at once: status, staged/unstaged diffs, log, branches, stashes
- The key insight: it replaces a dozen git commands with a visual interface. You don't need to learn new concepts — just where to find them.

## Phase 2: Demo sequence

Note: lazygit is interactive — narrate and have the user follow along in their own terminal.

### Opening
```bash
cd ~/dotfiles   # or any git repo
lazygit
```
Point out the layout:
- **Top-left**: Status (files changed)
- **Top-right**: Diff of selected file  
- **Bottom**: Commit log

### 1. Staging files
Navigate to the "Files" panel (top-left). Keys:
- `Space` — stage/unstage a file
- `a` — stage/unstage all files
- `Enter` — open file to stage individual hunks (like `git add -p`)

### 2. Committing
After staging, press `c` to commit. Type message + Enter. That's it.

### 3. Viewing the log
Press `4` or click the "Commits" panel. You can:
- Navigate commits with arrow keys
- Press `Enter` on a commit to see its diff
- Press `r` to reword a commit message
- Press `s` to squash into the commit below

### 4. Branching
Press `5` for Branches panel:
- `n` — new branch
- Space — checkout
- `M` — merge into current
- `r` — rebase current branch onto selected

### 5. Escaping / help
- `?` — show all keybindings for current panel
- `q` — quit lazygit
- `Esc` — go back / cancel

## Phase 3: Guided Practice

1. "Open `lazygit` in the `~/dotfiles` repo (or any repo with changes). Navigate to the Files panel. What files show as modified?"
2. "Stage a single file with `Space`. Check the diff panel on the right — what does it show? Now unstage it with `Space` again."
3. "Navigate to the Commits panel (press `4`). Press Enter on the most recent commit. What changed in that commit?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-lazygit-$(date +%s)
mkdir -p $SANDBOX
cd $SANDBOX
git init -q
git config user.email "student@example.com"
git config user.name "Student"

# Create initial state
echo "# My App" > README.md
echo "version = '1.0.0'" > config.py
echo "def main(): pass" > app.py
git add .
git commit -q -m "initial commit"

# Some history
echo "def helper(): return True" >> app.py
git add app.py
git commit -q -m "add helper function"

echo "DEBUG = True" >> config.py
git add config.py  
git commit -q -m "add debug flag"

# Uncommitted changes in various states
echo "# TODO: write docs" >> README.md           # modified
echo "def new_feature(): pass" > feature.py      # untracked
echo "SECRET_KEY = 'abc123'" > secrets.py        # untracked (should not be committed)
```

### Challenge
> "Open lazygit in `$SANDBOX`. You'll see: a modified README, an untracked feature file, and a secrets file that should NOT be committed. Your challenge: (1) stage only `README.md` and `feature.py` and commit them with message 'add feature and update readme'. (2) Without leaving lazygit, look at the second-most-recent commit — what changed? (3) Add `secrets.py` to a `.gitignore` file and commit that too. All inside lazygit."

**Solution:**
```
# (1) In Files panel:
# Navigate to README.md, press Space to stage
# Navigate to feature.py, press Space to stage
# Press c, type commit message, Enter

# (2) Navigate to Commits panel (4)
# Press down arrow to second commit
# Press Enter to view diff

# (3) Back in Files panel:
# Press Esc to return
# Navigate to files, press n to create new file... 
# Actually: quit lazygit, echo "secrets.py" > .gitignore, reopen lazygit
# Then stage .gitignore, commit "add gitignore"
```
