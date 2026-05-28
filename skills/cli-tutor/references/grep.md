# grep — Text Search

## Mental model
grep filters lines. Give it a pattern and a file (or stdin), and it returns only the lines that match. Everything else it does is variations on that core.

## Orient talking points
- The old way: open a file, Ctrl+F, scroll. Or `cat file | less` and `/search`. Terrible for multiple files.
- grep lets you search across dozens of files in milliseconds
- Key insight: grep works on lines. It doesn't know about words, sentences, or code structure — just patterns in lines.

## Phase 2: Demo sequence

### 1. Basic search
```bash
grep "error" /var/log/syslog 2>/dev/null | head -20
# or if no syslog:
grep "PATH" ~/.zshrc 2>/dev/null || grep "PATH" ~/.bashrc
```
Point out: matched lines are returned, others are dropped.

### 2. Case-insensitive + line numbers
```bash
grep -in "export" ~/.zshrc 2>/dev/null || grep -in "export" ~/.bashrc
```
`-i` = case-insensitive, `-n` = show line numbers. These two flags are used constantly.

### 3. Recursive search
```bash
grep -r "TODO" ~/dotfiles/
```
`-r` searches all files in a directory tree. Point out: shows `filename:line` for each match.

### 4. Invert match
```bash
grep -v "^#" ~/.zshrc 2>/dev/null | grep -v "^$" | head -20
```
`-v` returns lines that do NOT match. Here: strips comments and blank lines. Show the pipe chaining — grep's output is another list grep can filter.

### 5. Context lines
```bash
grep -n -A2 -B2 "alias" ~/.zshrc 2>/dev/null || grep -n -A2 -B2 "alias" ~/.bashrc
```
`-A2` = 2 lines after, `-B2` = 2 lines before. Essential for understanding matches in context.

## Phase 3: Guided Practice

1. "Run `grep -n 'alias' ~/.zshrc` (or `.bashrc`). How many aliases do you have defined?"
2. "Now add `-i` to make it case-insensitive. Does the count change?"
3. "Try `grep -r 'source' ~/dotfiles/ --include='*.zsh' 2>/dev/null`. The `--include` flag limits which files are searched. What did you find?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-grep-$(date +%s)
mkdir -p $SANDBOX/src/{api,utils} $SANDBOX/tests
cd $SANDBOX

cat > src/api/users.py << 'EOF'
# TODO @alice: add rate limiting here
def get_user(user_id):
    # Fetches a user from the database
    return db.query(f"SELECT * FROM users WHERE id={user_id}")

def create_user(email, password):
    # TODO @bob: validate email format
    # FIXME: password is stored in plaintext
    db.insert("users", {"email": email, "password": password})
EOF

cat > src/api/orders.py << 'EOF'
# TODO @alice: pagination not implemented
def list_orders():
    return db.query("SELECT * FROM orders")

def cancel_order(order_id):
    # TODO @charlie: send cancellation email
    db.update("orders", {"status": "cancelled"}, {"id": order_id})
EOF

cat > src/utils/logger.py << 'EOF'
import logging

# TODO: add log rotation
def setup_logger(name):
    logger = logging.getLogger(name)
    # FIXME @alice: log level should come from env var
    logger.setLevel(logging.DEBUG)
    return logger
EOF

cat > tests/test_users.py << 'EOF'
# TODO @bob: add more edge case tests
def test_create_user():
    # FIXME: this test doesn't actually test anything
    assert True
EOF
```

### Challenge
> "The sandbox has Python source files with TODO and FIXME comments. Some are tagged with a GitHub username (like `@alice`). Your challenge: **find every TODO or FIXME comment that has a username attached** (lines matching `TODO` or `FIXME` AND containing `@`). Show the filename, line number, and 1 line of context after each match. How many tagged items are there, and who has the most?"

**Solution:**
```bash
# Find lines with TODO/FIXME that also contain @username
grep -rn "TODO\|FIXME" src/ tests/ | grep "@"

# With context
grep -rn -A1 "TODO\|FIXME" src/ tests/ | grep -A1 "@"

# Count per person
grep -rn "TODO\|FIXME" src/ tests/ | grep -o "@[a-z]*" | sort | uniq -c | sort -rn
```
Answer: alice (3), bob (2), charlie (1).
