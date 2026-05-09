# Security Patterns — Complete Checklist

## 1. Secrets and Credentials

### Patterns to search for in source code

```
# Suspicious literal strings
password\s*=\s*["'][^"']+["']
api[_-]?key\s*=\s*["'][^"']+["']
secret\s*=\s*["'][^"']+["']
token\s*=\s*["'][^"']+["']
aws[_-]?access[_-]?key
AKIA[0-9A-Z]{16}                    # AWS Access Key ID
sk-[a-zA-Z0-9]{48}                  # OpenAI API Key
ghp_[a-zA-Z0-9]{36}                 # GitHub Personal Access Token
xox[bpas]-[a-zA-Z0-9-]+             # Slack Token

# Files that should NOT be in the repo
.env
.env.local
.env.production
*.pem
*.key
id_rsa
credentials.json
service-account*.json
```

### Files to review

- `.gitignore` — does it include `.env`, `*.key`, `*.pem`?
- `.github/workflows/*.yml` — secrets handled correctly?
- `docker-compose.yml` — passwords in plain text?
- `config/` — insecure default values?

### Secrets Scoring

| Finding | Score | Notes |
|----------|-------|-------|
| No secrets, complete .gitignore | 10 | |
| Correct .gitignore, no findings | 8 | |
| Obvious example/placeholder values | 6 | Acceptable if clearly fake |
| .env.example with real values | 2 | Concerning |
| Real secrets in code or history | 0 | **Override: automatic AVOID** |

---

## 2. GitHub Actions Security

### Dangerous patterns in workflows

```yaml
# DANGEROUS — excessive permissions
permissions: write-all

# DANGEROUS — runs fork PR code with secrets
on: pull_request_target
  # + checkout of PR + execution

# DANGEROUS — script injection
run: echo "${{ github.event.issue.title }}"
# Issue title executes as shell

# DANGEROUS — third-party Actions without SHA pin
uses: some-action@main
# Correct: uses: some-action@abc123sha

# SUSPICIOUS — artifact upload with broad paths
uses: actions/upload-artifact@v4
with:
  path: /
```

### Expected best practices

- [ ] Actions pinned to SHA (not to tags)
- [ ] Explicit and minimal `permissions` per job
- [ ] Don't use `pull_request_target` + PR checkout
- [ ] Secrets not exposed in logs (`::add-mask::`)
- [ ] Dependabot or Renovate configured
- [ ] Don't run unsanitized inputs in `run:`

---

## 3. Dynamic Code Execution

### Python

```python
eval()                    # Never on user input
exec()                    # Never on user input
subprocess.call(shell=True)  # Command injection
os.system()               # Command injection
pickle.loads()            # Unsafe deserialization
yaml.load()               # Without Loader= it's unsafe (use yaml.safe_load)
__import__()              # Dangerous dynamic loading
compile() + exec()        # Dynamic execution
```

### JavaScript/TypeScript

```javascript
eval()
Function()               // Function constructor
new Function()
setTimeout(string)       // With string, not function
setInterval(string)
document.write()
innerHTML =              // XSS
dangerouslySetInnerHTML   // React — requires justification
require(variable)        // Dynamic loading
child_process.exec()     // Without sanitization = injection
```

### Rust/Go

```rust
// Rust is generally safe, look for:
unsafe { }               // Unsafe blocks — require justification
std::process::Command    // Command execution — verify sanitization

// Go:
os/exec                  // Command execution
reflect                  // Excessive use of reflection
```

### Dynamic Execution Scoring

| Finding | Score |
|----------|-------|
| No dangerous patterns | 10 |
| Justified and sanitized use | 7 |
| Use without clear sanitization | 3 |
| eval/exec on user input without validation | 0 |

---

## 4. Dependencies

### Risk signals in dependencies

- **Typosquatting**: package with name similar to a popular one
- **Abandoned dependency**: no updates in >2 years
- **Single maintainer dependency**: package depended on by thousands but with 1 person
- **Unpinned version**: `*`, `latest`, broad ranges without lock file
- **Missing lock file**: no `package-lock.json`, `poetry.lock`, `Cargo.lock`

### Files to review

| Ecosystem | Manifest | Lock file |
|-----------|----------|-----------|
| Node.js | `package.json` | `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml` |
| Python | `requirements.txt` / `pyproject.toml` | `poetry.lock` / `uv.lock` |
| Rust | `Cargo.toml` | `Cargo.lock` |
| Go | `go.mod` | `go.sum` |
| Ruby | `Gemfile` | `Gemfile.lock` |

### Dependencies Scoring

| Finding | Score |
|----------|-------|
| Lock file present, pinned versions, few deps | 10 |
| Lock file present, reasonable deps | 7 |
| No lock file but pinned versions | 4 |
| Many deps, no lock, broad ranges | 2 |
| Dependencies known as compromised | 0 |

---

## 5. Insecure Default Configuration

Pattern inspired by Trail of Bits' `insecure-defaults`:

### Principle: Fail-Secure vs Fail-Open

- **Fail-secure** ✅: If configuration is missing, the system stops or uses safe values
- **Fail-open** ❌: If configuration is missing, the system starts with insecure defaults

### Concrete examples

```python
# ❌ FAIL-OPEN — Default JWT secret
JWT_SECRET = os.environ.get("JWT_SECRET", "changeme")

# ✅ FAIL-SECURE — Fails if no secret
JWT_SECRET = os.environ["JWT_SECRET"]  # KeyError if doesn't exist

# ❌ FAIL-OPEN — Debug mode by default
DEBUG = os.environ.get("DEBUG", "true")

# ✅ FAIL-SECURE — Debug off by default
DEBUG = os.environ.get("DEBUG", "false")

# ❌ FAIL-OPEN — Permissive CORS by default
CORS_ORIGINS = os.environ.get("CORS_ORIGINS", "*")

# ✅ FAIL-SECURE — No CORS by default
CORS_ORIGINS = os.environ.get("CORS_ORIGINS", "")
```

### Insecure Defaults Scoring

| Finding | Score |
|----------|-------|
| Fail-secure throughout the configuration | 10 |
| Mostly fail-secure, 1-2 documented exceptions | 7 |
| Mix of fail-secure and fail-open | 4 |
| Generalized fail-open | 1 |

---

## Phase 2 Calculation

```
P2 = weighted_average(
  secrets × 0.30,
  actions × 0.15,
  dynamic_exec × 0.20,
  dependencies × 0.20,
  insecure_defaults × 0.15
)
```
