# Supply Chain Signals — Evaluation Framework

## 1. Maintainer Identity

### GREEN signals

- Public profile with >2 year history
- Contributions to other known projects
- Conference/publication presence
- Employee of known company in the space
- Multiple maintainers with independent histories

### RED signals

- Recently created GitHub account (<6 months)
- No photo, bio, or links
- Only contributions to this repo
- Generic name without online presence
- Suspicious activity pattern (burst of commits then silence)

### Identity Scoring

| Signal | Score |
|-------|-------|
| Known org (FAANG, established startup, university) | 10 |
| Individual with verifiable public history | 8 |
| Individual with history but no external verification | 5 |
| New profile but with quality contributions | 4 |
| Anonymous profile or no history | 1 |

---

## 2. Organization and Funding

| Signal | Score |
|-------|-------|
| Company with clear business model (open core, SaaS) | 10 |
| Foundation or collective with public sponsors | 9 |
| Company project with demonstrated internal use | 8 |
| Individual with GitHub Sponsors / Open Collective | 6 |
| Personal project without funding | 3 |
| Unknown origin | 1 |

---

## 3. Security History

### What to search for (via web_search)

- `"<repo-name>" CVE` — does it have registered CVEs?
- `"<repo-name>" vulnerability` — vulnerability reports?
- `"<repo-name>" security advisory` — GitHub Security Advisories?
- SECURITY.md in the repo — does it have a disclosure policy?

### Scoring

| Signal | Score |
|-------|-------|
| SECURITY.md + history of fast patches + advisories | 10 |
| SECURITY.md present, no known incidents | 8 |
| No SECURITY.md but responds to security issues | 5 |
| Open CVEs without patch | 2 |
| Reported vulnerability ignored | 0 |

---

## 4. Publication and Distribution

### Official registries

| Signal | Score |
|-------|-------|
| Verified publisher on npm/PyPI + 2FA + provenance | 10 |
| Published on official registry with verified user | 8 |
| Published on official registry without verification | 5 |
| Only installable from GitHub (not on registries) | 3 |
| Only available as direct download/binary | 1 |

### Distribution integrity

- Lock file consistent with what's published?
- Are there discrepancies between code on GitHub and the published package?
- Does it use GitHub Actions to publish? (traceability)
- Does it have provenance attestation? (SLSA, Sigstore)

---

## 5. Code Review and Governance

| Signal | Score |
|-------|-------|
| Branch protection + mandatory PR reviews + CODEOWNERS | 10 |
| PR reviews on most changes | 7 |
| PRs exist but without consistent review | 4 |
| Direct push to main, no PRs | 1 |

### Additional indicators

- Is there a CONTRIBUTING.md with a defined process?
- Do external PRs receive review?
- Are there visible branch protection rules?
- Is there a documented release process?

---

## 6. Signing and Verification

| Signal | Score |
|-------|-------|
| Signed commits + signed releases + attestation | 10 |
| Signed releases / checksums | 7 |
| Partially signed commits | 4 |
| No signing of any kind | 2 |

---

## 7. Project Resilience

### Bus Factor Analysis

```
Bus factor = number of people who, if they disappear, the project dies

Bus factor 1: ONE person controls everything
  → Risk: illness, burnout, abandonment, account takeover
  → Mitigation: are there active forks? sufficient documentation for continuity?

Bus factor 2-3: Small team
  → Moderate but manageable risk
  → Verify: are the other contributors active recently?

Bus factor >5: Resilient project
  → Low risk of abandonment
```

### Signs of imminent abandonment

- Issues without response >90 days, increasing
- Community PRs without review >60 days
- Broken CI without fix
- Dependabot PRs accumulating without merge
- Last release >6 months with open issues

---

## Phase 3 Calculation

```
P3 = weighted_average(
  identity × 0.20,
  organization × 0.15,
  security_history × 0.25,
  publication × 0.15,
  code_review × 0.15,
  signing × 0.10
)
```

Apply bus factor override if applicable.
