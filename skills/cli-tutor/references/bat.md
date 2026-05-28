# bat — Better cat

## Mental model
bat is `cat` with syntax highlighting, line numbers, git diff markers, and a pager — all automatic. Use it anywhere you'd use `cat` to read a file.

## Orient talking points
- The old way: `cat file.py` dumps unstyled text. `less file.py` is better but no highlighting.
- bat gives you syntax highlighting for 150+ languages, line numbers, and shows git changes in the gutter — without any flags
- It auto-pages long files (like `less`) but exits immediately for short ones

## Phase 2: Demo sequence

### 1. Basic usage vs cat
```bash
cat ~/.zshrc | head -30
bat ~/.zshrc
```
Point out: syntax highlighting, line numbers, git gutter markers (if in a repo), the file header showing name and language.

### 2. Showing specific lines
```bash
bat --line-range 1:20 ~/.zshrc
```
Much cleaner than `sed -n '1,20p'`. Great for focusing on a section.

### 3. Plain output (for piping)
```bash
bat -p ~/.zshrc | grep "alias"
```
`-p` / `--plain` strips decoration. Use this when piping bat's output to another command.

### 4. Multiple files
```bash
bat ~/.zshrc ~/.bashrc 2>/dev/null | head -50
```
bat adds a header for each file. Useful for comparing configs.

### 5. Language override
```bash
echo '{"name": "Eric", "tool": "bat"}' | bat --language json
```
bat can highlight stdin with a specified language. Very handy for piped JSON, YAML, etc.

## Phase 3: Guided Practice

1. "Open a source file you have locally with `bat`. Notice the line numbers and syntax colors. Now open the same file with `cat`. What's missing?"
2. "Try `bat --line-range 1:10 ~/.zshrc`. What does it show? What changes if you use `--line-range 5:15`?"
3. "Run `curl -s https://api.github.com/users/octocat 2>/dev/null | bat --language json` (if you have internet). Or `echo '{\"a\":1}' | bat -l json`. What does the highlighting do for readability?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-bat-$(date +%s)
mkdir -p $SANDBOX
cd $SANDBOX
git init -q

cat > config.yaml << 'EOF'
server:
  host: localhost
  port: 8080
  debug: true

database:
  url: postgres://localhost/mydb
  pool_size: 10
  timeout: 30

logging:
  level: info
  format: json
  output: stdout
EOF

cat > main.py << 'EOF'
import os
import yaml

def load_config(path):
    with open(path) as f:
        return yaml.safe_load(f)

def main():
    config = load_config("config.yaml")
    print(f"Starting server on {config['server']['host']}:{config['server']['port']}")

if __name__ == "__main__":
    main()
EOF

git add config.yaml main.py
git commit -q -m "initial"
echo "  debug: false" >> config.yaml
echo "" >> main.py
echo "# TODO: add error handling" >> main.py
```

### Challenge
> "The sandbox has a config file and a Python script, with some uncommitted changes. Use `bat` to: (1) view `config.yaml` — do you see the git change markers in the gutter? (2) view only lines 1–5 of `main.py`. (3) pipe the YAML file through bat with explicit language detection. What command shows you the git diff markers? (Hint: they appear automatically for modified files in a git repo.)"

**Solution:**
```bash
# (1) git markers appear automatically
bat config.yaml

# (2) line range
bat --line-range 1:5 main.py

# (3) explicit language
cat config.yaml | bat --language yaml
# or
bat --language yaml config.yaml
```
