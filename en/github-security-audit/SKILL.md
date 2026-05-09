---
name: github-security-audit
description: >
  Security audit for GitHub repositories before integrating them into your stack. Seven dimensions:
  project health, code security, supply chain, code quality, license, MCP/plugin attack surface
  and external trust signals. Weighted scoring with ADOPT/EVALUATE/CAUTION/AVOID verdict and an
  HTML report. Inspired by Trail of Bits' supply-chain auditor and insecure-defaults frameworks.
  Activate with: audit repo, repo security, evaluate repo, evaluate library, evaluate dependency,
  review repo, supply chain, is this repo safe, security audit github, repo risk, evaluate MCP server,
  evaluate plugin, evaluate skill, evaluate integration. Also when the user shares a GitHub link and
  asks whether to integrate or trust it. Do NOT activate for own-codebase reviews or SEO audits.
---

# GitHub Security Audit — Risk Evaluation for Repositories

Systematic audit of GitHub repositories before integrating them into a production stack
or recommending them to clients. Inspired by Trail of Bits' frameworks
(supply-chain-risk-auditor, insecure-defaults, sharp-edges) but adapted for an
MCP-first agentic workflow.

## Philosophy

```
Don't evaluate repos with intuition. Evaluate with data.
Don't look for reasons to adopt it. Look for reasons NOT to adopt it.
If you can't find reasons to reject it, then it can come in.
```

**Golden rule**: A repo that solves a problem your stack already covers
adds NO value, regardless of its quality. Evaluate first whether there is
a real need before spending time on the full analysis.

---

## Two execution modes

| Mode | Command | Phases | Tool calls | Use case |
|------|---------|---------|------------|-----|
| **Full** | `audit repo <url>` | 7 phases | 10-20 | Comprehensive pre-integration review |
| **Quick** | `audit repo quick <url>` | Phases 0+1+2 | 5-8 | Fast triage or first impression |

---

## STEP 0 — Need Gate (MANDATORY, before ANY analysis)

Before analyzing security, answer:

1. **What problem does this repo solve?** (define in one sentence)
2. **Is that problem already covered by your current stack?** (MCPs, skills, tools)
3. **Does it bring genuinely new capability?**

If the answer to (2) is YES and to (3) is NO → **VERDICT: SKIP** — do not continue.
Inform the user with the justification and suggest the existing tool.

If there's reasonable doubt or the user insists → continue with Phase 1.

---

## PHASE 1 — Project Health

**Sources**: web_fetch of the GitHub repo page, GitHub API via web_fetch.

Collect:

| Metric | How to obtain | GREEN threshold | RED threshold |
|---------|---------------|--------------|-------------|
| Stars | Repo page | >500 | <50 |
| Forks | Repo page | >50 | <5 |
| Open vs closed issues | Repo tabs | Close ratio >60% | <30% |
| Last commit | Commits page | <30 days | >180 days |
| Contributors | Contributors tab | >5 | 1 (bus factor) |
| Releases | Releases tab | Semantic versioning | No releases |
| README quality | Content | Complete docs, examples | No README or stub |
| CI/CD | GitHub Actions / badges | Automated tests | No CI |

**Scoring**: Each metric 0-10, total weight = 20% of final score.

See `references/health-scoring.md` for the detailed scoring table.

---

## PHASE 2 — Code Security

**Sources**: web_fetch of key repo files.

### 2.1 Static analysis (without executing code)

Review via web_fetch:

- **Hardcoded secrets**: search source code for API key patterns, tokens, passwords
  (`password =`, `api_key =`, `secret`, `token`, committed `.env` files)
- **Known vulnerable dependencies**: review `package.json`, `requirements.txt`,
  `Cargo.toml`, `go.mod` — cross-reference versions with known CVEs via web_search
- **Excessive permissions**: in GitHub Actions workflows (`.github/workflows/*.yml`),
  look for `permissions: write-all`, `pull_request_target`, secrets in fork PRs
- **Dangerous dynamic execution**: `eval()`, `exec()`, `subprocess.call(shell=True)`,
  `dangerouslySetInnerHTML`, `innerHTML`, unsafe deserialization
- **Insecure default configuration**: following Trail of Bits' insecure-defaults
  pattern — does the repo work insecurely if you don't configure anything?

### 2.2 Transitive dependencies

- How many direct dependencies does it have?
- Are there dependencies with a single maintainer?
- Are there dependencies that have been compromised previously?

**Scoring**: 0-10, weight = 25% of final score.

See `references/security-patterns.md` for the complete checklist.

---

## PHASE 3 — Supply Chain

**Sources**: web_search of maintainer profile, repo history.

| Signal | GREEN | RED |
|-------|-------|------|
| Identifiable maintainer(s) | Real person/org, public history | New account, no history |
| Backed organization | Known company, funding | Anonymous, no context |
| Security history | Vulnerabilities patched quickly | Open CVEs, no response |
| Publication on official registries | npm/PyPI with 2FA, verified publisher | No official publication |
| Code review on PRs | PRs reviewed before merge | Direct push to main |
| SECURITY.md | Exists with disclosure policy | Doesn't exist |
| Signed commits/releases | GPG signatures | Unsigned |

**Scoring**: 0-10, weight = 20% of final score.

See `references/supply-chain-signals.md` for detailed analysis.

---

## PHASE 4 — Code Quality

**Sources**: web_fetch of representative files.

- **Test coverage**: Mentioned in README/CI? What percentage?
- **Type hints / static types**: Does it use typing?
- **API documentation**: Docstrings/JSDoc/rustdoc?
- **Architecture**: Modular or monolithic? Separation of concerns?
- **Error handling**: Generic try/catch or specific handling?
- **Logging**: Structured logging?

**Scoring**: 0-10, weight = 10% of final score.

---

## PHASE 5 — License and Legal Compatibility

**Sources**: web_fetch of LICENSE file.

| License | General compatibility | Notes |
|----------|---------------------|-------|
| MIT, BSD-2, BSD-3 | ✅ Full | No restrictions |
| Apache-2.0 | ✅ Full | Favorable patent clause |
| CC-BY-4.0, CC-BY-SA-4.0 | ⚠️ Partial | OK for skills/docs, not for code |
| GPL-2.0, GPL-3.0 | ⚠️ Conditional | Copyleft — evaluate if dependency or integration |
| AGPL-3.0 | ❌ High risk | Network copyleft — affects SaaS |
| SSPL, BSL, proprietary | ❌ Don't use | Commercial restrictions |
| No license | ❌ Don't use | Default copyright, no permissions |

**Scoring**: 0-10, weight = 10% of final score.

---

## PHASE 6 — MCP/Plugin/Skill Attack Surface

**Applies when**: the repo is an MCP server, Claude Code plugin, skill, integration tool,
or any software that runs with access to client data.

Inspired by Trail of Bits' agentic-actions-auditor and sharp-edges.

### MCP/Plugin Checklist

- [ ] What permissions does it request? (filesystem, network, env vars, secrets)
- [ ] Does it send data to third parties? (telemetry, analytics, external APIs)
- [ ] Does it store data locally? Where? Encrypted?
- [ ] Does it have hooks that run automatically? (pre/post tool use)
- [ ] Is the MCP server's code auditable? Or is it a binary/remote service?
- [ ] Does it use `eval()`, `exec()`, or dynamic execution of user-provided code?
- [ ] Does it validate LLM inputs before executing? (prompt injection surface)
- [ ] Does it have rate limiting?
- [ ] What happens if it fails? (fail-open vs fail-secure)

### Rationalizations to Reject

(Inspired by Trail of Bits' "Rationalizations to Reject")

- "It's open source, so it's secure" → NO. Open source ≠ audited.
- "It has many stars" → NO. Stars measure popularity, not security.
- "Company X uses it" → NO. Unless company X has done a public audit.
- "It's read-only" → VERIFY. Really? Doesn't it write logs, cache, temp files?
- "I'll only use it internally" → Doesn't reduce risk if it touches client data.
- "The maintainer seems trustworthy" → VERIFY with data, not gut feeling.

**Scoring**: 0-10, weight = 15% of final score (0% if not applicable, redistributed).

---

## PHASE 7 — External Trust Signals

**Sources**: web_search.

- Has it been mentioned in security publications? (positive or negative)
- Does it have third-party audits?
- Does it appear on curated quality lists? (awesome-X, Trail of Bits skills-curated)
- Has it been reported as malicious at any point?
- Does it have a bug bounty program or SECURITY.md?

**Scoring**: 0-10, weight = bonus (doesn't subtract, only adds up to +5 points to total).

---

## Final Score Calculation

```
SCORE = (P1 × 0.20) + (P2 × 0.25) + (P3 × 0.20) + (P4 × 0.10) + (P5 × 0.10) + (P6 × 0.15) + bonus_P7

If P6 doesn't apply:
SCORE = (P1 × 0.24) + (P2 × 0.29) + (P3 × 0.23) + (P4 × 0.12) + (P5 × 0.12) + bonus_P7
```

### Verdict

| Score | Verdict | Action |
|-------|-----------|--------|
| ≥7.5 | **ADOPT** 🟢 | Integrate with confidence. Monitor updates. |
| 5.0–7.4 | **EVALUATE** 🟡 | Integrate with precautions. Document accepted risks. Review in 90 days. |
| 3.0–4.9 | **CAUTION** 🟠 | Only if no alternative. Isolate. Deep audit before production. |
| <3.0 | **AVOID** 🔴 | Do not integrate. Find alternative. |

### Override rules (override numeric scoring)

- No license → automatic **AVOID**
- AGPL/SSPL → automatic **AVOID** (except internal use without SaaS)
- Hardcoded secrets in repo → automatic **AVOID**
- Single maintainer + last commit >1 year → maximum **CAUTION**
- Unsanitized dynamic execution in MCP → automatic **AVOID**

---

## Deliverable

Generate an interactive HTML report with:

1. **Header**: Repo name, URL, date, verdict with color badge
2. **Executive summary**: 3-5 sentences with key findings
3. **Radar chart**: The 7 dimensions in a radial chart (or 6 if P6 doesn't apply)
4. **Detail per phase**: Table with metrics, values found, scoring
5. **Critical findings**: List of issues requiring attention (if any)
6. **Rejected rationalizations**: If detected during analysis
7. **Final verdict**: Score + verdict + recommended action
8. **Stack comparison**: What current tool covers similar functionality?

Save in the working/output directory.

---

## When to Use

- Before integrating any repo/library/MCP/plugin/skill into your stack
- When a client asks about a tool's security
- When evaluating a new dependency for a project
- When the user shares a GitHub link asking whether to use it
- To audit third-party MCPs or skills before installing them

## When NOT to Use

- For SEO audits of websites
- For analyzing your own code (use your dev/test workflow)
- For creating new skills (use skill-creator)
- To evaluate tools that are not open source / have no public repo

---

## References

Load on demand:

- `references/health-scoring.md` — Detailed scoring table per health metric
- `references/security-patterns.md` — Complete security pattern checklist
- `references/supply-chain-signals.md` — Supply chain evaluation framework
