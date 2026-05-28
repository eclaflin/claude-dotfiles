# zoxide — Smarter cd

## Mental model
zoxide learns which directories you visit and lets you jump to them by typing just part of the name — no full paths. It's `cd` with a built-in memory of where you've been.

## Orient talking points
- The old way: `cd ~/projects/work/big-company/backend/services/auth/` — painful to type, impossible to remember exactly
- zoxide ranks directories by how often and recently you visit them
- You just type `z auth` and it jumps to the most likely match

## Phase 2: Demo sequence

### 1. Basic jump
```bash
# First, check what's in zoxide's database
zoxide query --list | head -20
```
Show the list of remembered directories. If empty (first run), explain that it populates as you use `z` instead of `cd`.

### 2. Jump by partial name
```bash
z dotfiles   # or whatever directory name makes sense given their history
```
Contrast with typing the full path. If their database is empty, demo with `cd` + `zoxide add`:
```bash
zoxide add ~/dotfiles
zoxide add ~/.config
zoxide query --list
z dot   # jumps to dotfiles
```

### 3. Interactive mode (requires fzf)
```bash
zi   # or: z -i
```
Opens fzf to pick from your history interactively. The best of both worlds — zoxide's ranking + fzf's fuzzy search. Note: requires fzf to be installed.

### 4. Query without jumping
```bash
zoxide query dotfiles    # shows what z would jump to, without jumping
zoxide query --list | sort -k1 -rn | head -10   # top 10 by score
```
Useful for understanding why zoxide picks what it picks.

### 5. Subdirectory targeting
```bash
z config nvim   # can match on multiple path segments
```
Multiple words narrow to paths that contain both. So `z work auth` would match `.../work/.../auth/`.

## Phase 3: Guided Practice

1. "Run `zoxide query --list` — how many directories does it know about? Does the list make sense given where you spend time?"
2. "Navigate to a directory you use often using `z` with just the last part of the name. Did it go to the right place?"
3. "Try `zi` (if fzf is installed). Type part of a directory name. What does the ranked list look like?"

## Phase 4: Sandbox Setup

```bash
# Create a set of directories with similar names to force disambiguation
SANDBOX=/tmp/cli-tutor-zoxide-$(date +%s)
mkdir -p $SANDBOX/{projects,archive}/{frontend,backend,api}
mkdir -p $SANDBOX/work/acme/{frontend,backend}
mkdir -p $SANDBOX/personal/side-project/{frontend,docs}

# Register them all with zoxide, with fake scores
zoxide add $SANDBOX/projects/frontend
zoxide add $SANDBOX/projects/backend
zoxide add $SANDBOX/work/acme/frontend
zoxide add $SANDBOX/work/acme/backend
zoxide add $SANDBOX/personal/side-project/frontend
zoxide add $SANDBOX/personal/side-project/docs

# Bump scores for some to simulate real usage
for i in $(seq 1 5); do zoxide add $SANDBOX/work/acme/backend; done
for i in $(seq 1 3); do zoxide add $SANDBOX/projects/frontend; done
```

### Challenge
> "I've added 6 directories to your zoxide database — some with similar names. Your challenge: (1) use `zoxide query --list` to see their scores. (2) use `z frontend` — where does it land, and why? (3) use a two-word query to jump specifically to `$SANDBOX/personal/side-project/frontend` without typing the full path. (4) use `zi` to interactively pick `$SANDBOX/projects/backend`."

**Solution:**
```bash
# (1)
zoxide query --list

# (2) lands on the highest-scored frontend dir (probably work/acme/backend jumped... wait)
# Actually: z frontend lands on projects/frontend (score 3) or side-project/frontend (score 1)
# whichever has higher score

# (3) narrow with two words
z personal frontend
# or
z side frontend

# (4)
zi   # then type "projects back" to narrow
```
