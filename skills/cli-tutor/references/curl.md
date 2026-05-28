# curl — HTTP Client

## Mental model
curl sends HTTP requests and shows you the response. It's the simplest possible way to talk to an API, download a file, or test a server — without writing any code.

## Orient talking points
- The old way: write a script, open Postman, or just guess
- curl lets you make any HTTP request from the command line and see exactly what comes back
- It's the universal tool for API testing, debugging, and scripting HTTP workflows

## Phase 2: Demo sequence

### 1. Basic GET request
```bash
curl https://httpbin.org/get
```
httpbin is a free HTTP testing service — it echoes back what you sent. Point out the JSON response with headers, IP, etc.

### 2. Verbose mode (see headers)
```bash
curl -v https://httpbin.org/get 2>&1 | head -40
```
`-v` shows the full request/response including headers. Lines starting with `>` are sent, `<` are received. Critical for debugging.

### 3. POST with JSON body
```bash
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d '{"name": "Eric", "tool": "curl"}'
```
Point out: `-X` sets method, `-H` adds a header, `-d` is the body. The response echoes back your JSON under `"json"`.

### 4. Save to file + follow redirects
```bash
curl -L -o /tmp/curl-test.html https://example.com
wc -c /tmp/curl-test.html
```
`-L` follows redirects (very common — most URLs redirect), `-o` saves to a file instead of printing.

### 5. Authentication
```bash
curl -u username:password https://httpbin.org/basic-auth/username/password
```
Basic auth. For Bearer tokens: `-H "Authorization: Bearer <token>"`. For API keys: `-H "X-API-Key: <key>"`.

### 6. Show only status code
```bash
curl -o /dev/null -s -w "%{http_code}\n" https://httpbin.org/status/404
curl -o /dev/null -s -w "%{http_code}\n" https://httpbin.org/get
```
`-o /dev/null` discards body, `-s` silent mode, `-w` writes formatted output. Great for scripting.

## Phase 3: Guided Practice

1. "Run `curl https://httpbin.org/get`. Find your IP address in the response. What key is it under?"
2. "Add `-s | python3 -m json.tool` to pretty-print the JSON. (Or `| jq .` if you have jq.) What does the output look like now?"
3. "Try `curl -X POST https://httpbin.org/post -H 'Content-Type: application/json' -d '{\"hello\": \"world\"}'`. Find your JSON in the response. Which field contains it?"

## Phase 4: Sandbox Setup

```bash
# The sandbox is the httpbin.org API — no local setup needed.
# We'll also use a local file as a "mock API"
SANDBOX=/tmp/cli-tutor-curl-$(date +%s)
mkdir -p $SANDBOX
cd $SANDBOX

# Create a fake API response to work with
cat > response.json << 'EOF'
{
  "users": [
    {"id": 1, "name": "Alice", "role": "admin", "active": true},
    {"id": 2, "name": "Bob", "role": "user", "active": false},
    {"id": 3, "name": "Charlie", "role": "user", "active": true}
  ],
  "total": 3,
  "page": 1
}
EOF
```

### Challenge
> "Three tasks using curl against httpbin.org: (1) Make a POST request that sends your name and the current date as JSON. Confirm your data appears in the response. (2) Make a request that deliberately gets a 401 response — use the `/basic-auth/user/pass` endpoint but with wrong credentials. Show just the HTTP status code (no body). (3) Fetch `https://httpbin.org/delay/1` and measure how long it takes — use curl's `-w` flag with `%{time_total}`. What timing fields are available in `-w`?"

**Solution:**
```bash
# (1)
curl -X POST https://httpbin.org/post \
  -H "Content-Type: application/json" \
  -d "{\"name\": \"Eric\", \"date\": \"$(date +%Y-%m-%d)\"}"

# (2) Wrong credentials → 401
curl -o /dev/null -s -w "%{http_code}\n" -u user:wrongpass \
  https://httpbin.org/basic-auth/user/pass

# (3) Timing
curl -o /dev/null -s \
  -w "Total: %{time_total}s\nDNS: %{time_namelookup}s\nConnect: %{time_connect}s\n" \
  https://httpbin.org/delay/1
```
