# Example Audit Report

This is a worked example showing the kind of output you can expect from the skill.
The repo audited here is **fictitious** — illustrative numbers only.

---

## Audit: `acme-org/super-mcp-tool`

**URL**: `https://github.com/acme-org/super-mcp-tool`
**Date**: 2026-04-15
**Mode**: Full audit

---

### Executive summary

`super-mcp-tool` is a Model Context Protocol server that exposes a third-party scheduling
API to LLMs. The project shows healthy activity (1.2k stars, weekly commits, 8 contributors)
and uses Apache-2.0, but it requests broad filesystem and network permissions, executes
LLM-provided strings via `subprocess.run(... shell=True)` in one code path, and ships
without a SECURITY.md. Verdict: **EVALUATE 🟡** with mandatory sandboxing before any
production use.

---

### Step 0 — Need Gate

| Question | Answer |
|----------|--------|
| What problem does it solve? | Lets Claude read/write to a calendar API |
| Already covered in stack? | Partially — current Calendar MCP covers reads but not writes |
| Genuinely new capability? | Yes (write path) |

**Decision**: Continue with audit.

---

### Phase scores

| Phase | Score | Notes |
|-------|------:|-------|
| 1. Project Health | 8.2 | 1.2k stars, 8 contributors, last commit 3 days ago, full README, CI present |
| 2. Code Security | 4.8 | `shell=True` in `runner.py:142` with templated user input. Dependencies pinned, lock file present, no secrets found. |
| 3. Supply Chain | 7.0 | Maintainer is identifiable employee at known SaaS company. Releases unsigned. No SECURITY.md. |
| 4. Code Quality | 7.5 | 78% test coverage, type hints throughout, structured logging |
| 5. License | 10.0 | Apache-2.0 |
| 6. MCP Surface | 3.5 | Requests filesystem write to `~/.config/`, network to `*.calendar-vendor.com`, dynamic execution path identified |
| 7. External Trust (bonus) | +1 | Mentioned positively in two newsletters; no third-party audit |

```
Final score = (8.2 × 0.20) + (4.8 × 0.25) + (7.0 × 0.20) + (7.5 × 0.10) + (10.0 × 0.10) + (3.5 × 0.15) + 1.0
            = 1.64 + 1.20 + 1.40 + 0.75 + 1.00 + 0.525 + 1.0
            = 7.515 → ADOPT borderline, but...
```

### Override check

- Unsanitized dynamic execution in an MCP path? **YES** (`runner.py:142`).
- Override rule fires: **AVOID automatic for production MCP use**, downgraded to **EVALUATE** for sandboxed/non-production evaluation.

---

### Critical findings

1. **`runner.py:142` — `subprocess.run(f"...{user_arg}...", shell=True)`.**
   LLM-provided arguments reach the shell without sanitization. Blocking issue for any production deployment that exposes the MCP to untrusted prompts.

2. **No SECURITY.md.** No documented disclosure path. Accepted risk for evaluation; unacceptable for long-term production.

3. **Releases unsigned.** Combined with broad filesystem permissions, supply-chain compromise of releases would be hard to detect.

---

### Rationalizations rejected during analysis

- "It has 1.2k stars so it's safe" → rejected. Stars measure visibility, not safety.
- "The maintainer works at a known company so it's vetted" → rejected. Personal projects of company employees do not inherit corporate security review.

---

### Stack comparison

The current Calendar MCP in the stack covers read operations safely. This repo adds writes
but at the cost of a known unsanitized execution path. **Recommendation**: file an upstream
PR to fix `runner.py:142`, or fork and patch internally before considering adoption.

---

### Final verdict

🟡 **EVALUATE** — with conditions:

1. Patch or fork to remove `shell=True` path before any client-data exposure
2. Sandbox via container with minimal filesystem and network egress
3. Re-audit in 90 days; specifically check whether SECURITY.md and signed releases land

---

*This example is illustrative. Real audits produce HTML output with a radar chart and per-phase tables; this Markdown version is the same content rendered text-only.*
