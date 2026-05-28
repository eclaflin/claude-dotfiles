# fzf — Fuzzy Finder

## Mental model
fzf is a fuzzy filter that wraps any list. Pipe anything into it, get an interactive search, get the selection back out. It's a universal "pick one from many" primitive.

## Orient talking points
- The old way: `ls | grep partial-name`, scroll, guess, retype
- fzf wraps that whole workflow into an interactive interface
- Core use: standalone file finder. Real power: piped into other commands and shell integrations

## Phase 2: Demo sequence

### 1. Basic invocation
```bash
# Run fzf standalone — fuzzy-searches files in current dir tree
fzf
```
Walk the user through: typing narrows results, arrow keys navigate, Enter selects. Escape to quit.

### 2. Preview window
```bash
fzf --preview 'cat {}'
```
Shows file contents as you navigate. Point out: `{}` is the placeholder for the current selection.

### 3. Piping a list into fzf
```bash
ls /usr/bin | fzf
```
fzf works on *any* list. This is the key insight — it's not just a file finder.

### 4. Shell integration (history search)
```bash
# Ctrl+R in the shell — fzf-enhanced history search
# (demonstrate by pressing Ctrl+R)
```
Also mention: Ctrl+T for file insertion, Alt+C for cd. These come from the fzf shell integration.

## Phase 3: Guided Practice

1. "Run `fzf` from your home directory. Type a few letters of a filename you know exists somewhere. Select it and hit Enter — what gets printed?"
2. "Now try: `echo -e 'apple\nbanana\ncherry\ndate' | fzf`. What happens when you type 'an'?"
3. "Add `--preview 'wc -l {}'` to the file search. What does it show?"

## Phase 4: Sandbox Setup

```bash
mkdir -p /tmp/cli-tutor-fzf-$(date +%s)
cd /tmp/cli-tutor-fzf-*/

# Create a fake project with various files
mkdir -p src/{api,components,utils} tests docs
touch src/api/{users.ts,orders.ts,auth.ts,payments.ts}
touch src/components/{Button.tsx,Modal.tsx,Table.tsx,Form.tsx,Header.tsx}
touch src/utils/{format.ts,validate.ts,logger.ts}
touch tests/{users.test.ts,orders.test.ts,auth.test.ts}
touch docs/{api.md,setup.md,contributing.md}
```

### Challenge
> "The sandbox has a fake project with 16 files across several directories. Your challenge: use `fzf` with a preview window showing the first 5 lines of each file to navigate to exactly `src/components/Modal.tsx` — without typing the full path. Then, pipe just the `src/components/` directory contents into fzf and select a file. What command did you use for the second part?"

**Solution:**
```bash
# Part 1
fzf --preview 'head -5 {}'
# Part 2
ls src/components/ | fzf
# or
find src/components -type f | fzf
```
