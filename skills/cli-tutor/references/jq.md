# jq — JSON Processor

## Mental model
jq is a query language for JSON. Pipe JSON into it, extract fields, transform the structure, filter arrays. It's what grep/sed/awk are for text — but for JSON.

## Orient talking points
- The old way: `python3 -c "import json, sys; d=json.load(sys.stdin); print(d['key'])"` — painful
- jq handles any JSON transformation in one line, with readable syntax
- The key insight: jq programs are filters. `.` is the whole input. `.key` extracts a field. Filters chain with `|`.

## Phase 2: Demo sequence

### 1. Pretty-print JSON
```bash
echo '{"name":"Eric","tools":["fzf","jq","bat"]}' | jq .
```
`.` is the identity filter — outputs the whole input, but pretty-printed. This alone is worth having jq.

### 2. Extract a field
```bash
echo '{"name":"Eric","tools":["fzf","jq","bat"]}' | jq '.name'
echo '{"name":"Eric","tools":["fzf","jq","bat"]}' | jq '.tools'
echo '{"name":"Eric","tools":["fzf","jq","bat"]}' | jq '.tools[0]'
```
`.key` extracts a field. `.array[index]` indexes into an array.

### 3. Real API data
```bash
curl -s https://api.github.com/users/octocat | jq '{login: .login, name: .name, repos: .public_repos}'
```
Extract just the fields you care about and reshape into a new object.

### 4. Filter arrays
```bash
echo '[{"name":"Alice","active":true},{"name":"Bob","active":false},{"name":"Charlie","active":true}]' | \
  jq '[.[] | select(.active == true)]'
```
`.[]` iterates an array. `select(condition)` keeps only matching items. Wrap in `[...]` to collect back into an array.

### 5. Transform and reshape
```bash
curl -s https://api.github.com/users/octocat/repos 2>/dev/null | \
  jq '[.[] | {name: .name, stars: .stargazers_count, language: .language}] | sort_by(.stars) | reverse | .[0:5]'
```
Extract, reshape, sort, and limit — all in one pipeline. (If no internet: use a local file.)

## Phase 3: Guided Practice

```bash
# Setup data for practice
cat > /tmp/jq-practice.json << 'EOF'
{
  "team": "platform",
  "members": [
    {"name": "Alice", "role": "lead", "languages": ["python", "go"], "yoe": 8},
    {"name": "Bob", "role": "engineer", "languages": ["javascript", "typescript"], "yoe": 3},
    {"name": "Charlie", "role": "engineer", "languages": ["python", "rust"], "yoe": 5},
    {"name": "Diana", "role": "lead", "languages": ["go", "java"], "yoe": 10}
  ]
}
EOF
```

1. "Run `cat /tmp/jq-practice.json | jq '.team'`. Now extract just the `members` array."
2. "Get just the names: `jq '.members[].name'`. Now wrap it in array syntax `[.members[].name]` — what changes?"
3. "Filter to only members with `yoe` greater than 4: `jq '[.members[] | select(.yoe > 4)]'`. How many members qualify?"

## Phase 4: Sandbox Setup

```bash
SANDBOX=/tmp/cli-tutor-jq-$(date +%s)
mkdir -p $SANDBOX
cd $SANDBOX

cat > orders.json << 'EOF'
[
  {"id": 1001, "customer": "Alice", "status": "shipped", "total": 89.99, "items": 3},
  {"id": 1002, "customer": "Bob", "status": "pending", "total": 234.50, "items": 1},
  {"id": 1003, "customer": "Alice", "status": "delivered", "total": 45.00, "items": 2},
  {"id": 1004, "customer": "Charlie", "status": "pending", "total": 789.99, "items": 5},
  {"id": 1005, "customer": "Bob", "status": "shipped", "total": 12.99, "items": 1},
  {"id": 1006, "customer": "Diana", "status": "cancelled", "total": 150.00, "items": 2},
  {"id": 1007, "customer": "Alice", "status": "pending", "total": 67.50, "items": 1}
]
EOF
```

### Challenge
> "You have an orders dataset at `$SANDBOX/orders.json`. Three tasks: (1) Extract all pending orders and show only their `id`, `customer`, and `total`. (2) Find Alice's total spending across all her orders (hint: `map(.total) | add`). (3) Group the orders by status and count how many are in each group (hint: `group_by(.status)`). Which status has the most orders?"

**Solution:**
```bash
# (1)
jq '[.[] | select(.status == "pending") | {id, customer, total}]' orders.json

# (2) Alice's total
jq '[.[] | select(.customer == "Alice") | .total] | add' orders.json
# → 202.49

# (3) Group by status
jq 'group_by(.status) | map({status: .[0].status, count: length})' orders.json
# pending: 3, shipped: 2, delivered: 1, cancelled: 1
```
