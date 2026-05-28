# ripgrep (rg) — Fast Recursive Search

## Mental model
ripgrep is grep that works the way you wish grep did: recursive by default, respects `.gitignore`, shows filenames automatically, and is dramatically faster. For code search, it's strictly better than grep.

## Orient talking points
- The old way: `grep -r --include="*.py" "pattern" .` — verbose flags, ignores nothing
- rg is recursive by default, ignores `.git`, `node_modules`, and `.gitignore` files automatically
- Same syntax as grep, but faster and smarter. For anything in a codebase, reach for rg first.

## Phase 2: Demo sequence

### 1. Basic search (recursive, automatic)
```bash
rg "TODO" ~/dotfiles/
```
No `-r` needed. It searches recursively by default, shows filename:line:match.

### 2. File type filtering
```bash
rg "alias" ~/dotfiles/ --type sh
# or
rg "alias" ~/dotfiles/ -t zsh
```
`--type` / `-t` limits to a language/extension. `rg --type-list` shows all supported types.

### 3. Case insensitive + word boundary
```bash
rg -i "path" ~/dotfiles/
rg -w "path" ~/dotfiles/   # whole word only
```
`-w` matches whole words — no partial matches. So `rg -w path` won't match `filepath`.

### 4. Show context
```bash
rg -C 2 "export" ~/dotfiles/
```
`-C 2` shows 2 lines of context on each side. `-A` / `-B` for after/before only.

### 5. Count matches per file
```bash
rg -c "alias" ~/dotfiles/
```
`-c` shows match count per file, not the lines themselves. Great for a quick overview.

### 6. Glob patterns
```bash
rg "source" ~/dotfiles/ --glob "*.zsh" --glob "!*.disabled"
```
`--glob` / `-g` filters by filename pattern. Prefix with `!` to exclude.

## Phase 3: Guided Practice

1. "Run `rg TODO ~/dotfiles/`. How many TODOs are in your dotfiles? (Or pick any directory you have code in.)"
2. "Try `rg -c '' ~/dotfiles/` — counts all lines in all files. Now add `--type sh` to limit to shell files. What changes?"
3. "Search for your own name in `~/dotfiles/` with `rg -i 'eric' ~/dotfiles/`. Add `-l` to show just filenames (not the matching lines). How many files mention your name?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-rg-$(date +%s)
mkdir -p $SANDBOX/src/{api,models,middleware} $SANDBOX/tests $SANDBOX/docs
cd $SANDBOX
git init -q

# Source files
cat > src/api/users.py << 'EOF'
from models.user import User

# TODO: add pagination
def list_users(db, limit=100):
    return db.query(User).limit(limit).all()

def get_user(db, user_id: int):
    # FIXME: no 404 handling
    return db.query(User).filter(User.id == user_id).first()

def delete_user(db, user_id: int):
    user = get_user(db, user_id)
    db.delete(user)
    db.commit()
EOF

cat > src/api/auth.py << 'EOF'
import hashlib

# TODO: use bcrypt instead of sha256
def hash_password(password: str) -> str:
    return hashlib.sha256(password.encode()).hexdigest()

def verify_password(password: str, hashed: str) -> bool:
    return hash_password(password) == hashed

# FIXME: tokens never expire
def generate_token(user_id: int) -> str:
    return f"token_{user_id}_{hash_password(str(user_id))}"
EOF

cat > src/models/user.py << 'EOF'
from dataclasses import dataclass

@dataclass
class User:
    id: int
    email: str
    # TODO: add created_at field
    password_hash: str
    is_active: bool = True
EOF

cat > src/middleware/logging.py << 'EOF'
import logging

# TODO: structured logging (JSON format)
def setup_logging(level="INFO"):
    logging.basicConfig(level=level)
    return logging.getLogger(__name__)
EOF

echo "node_modules/" > .gitignore
echo "*.pyc" >> .gitignore
mkdir -p node_modules/fake-package
echo "this should be ignored" > node_modules/fake-package/index.js
```

### Challenge
> "The sandbox is a small Python project with various issues scattered in comments. Three tasks: (1) Find all `FIXME` comments — how many are there and in which files? (2) Find all `TODO` comments that mention a specific technology or tool name (grep for both `TODO` and any word ending in 't' on the same line, i.e. `rg 'TODO.*\w+t\b'`). (3) Count how many lines of actual Python code exist (not counting blank lines or comment-only lines) using rg's `-c` flag with a pattern that matches code lines. Does rg search inside `node_modules/`?"

**Solution:**
```bash
# (1) FIXMEs
rg FIXME src/
# 2 FIXMEs in auth.py

# (2) TODOs mentioning a tool
rg 'TODO.*\w+t\b' src/
# matches "bcrypt", "created_at" etc.

# (3) Count code lines (non-blank, non-comment)
rg -c '^\s*[^#\s]' src/ -t py
# rg does NOT search node_modules/ — it respects .gitignore
```
