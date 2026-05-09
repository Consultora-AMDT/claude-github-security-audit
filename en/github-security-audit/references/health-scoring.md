# Health Scoring — Detailed Score Table

## Stars

| Range | Score | Notes |
|-------|-------|-------|
| >10,000 | 10 | Established project, wide adoption |
| 5,000–10,000 | 9 | Very popular, active community |
| 1,000–5,000 | 8 | Popular, demonstrated traction |
| 500–1,000 | 7 | Good adoption |
| 200–500 | 5 | Moderate adoption |
| 50–200 | 3 | Niche or emerging project |
| <50 | 1 | New or unknown project |

**Note**: Stars alone are NOT an indicator of security or quality. They are an indicator of visibility.

## Forks

| Range | Score | Notes |
|-------|-------|-------|
| >500 | 10 | Extensively forked, signal of real use |
| 100–500 | 8 | Significant use |
| 50–100 | 6 | Moderate use |
| 10–50 | 4 | Limited use |
| <10 | 2 | Minimal use |
| 0 | 0 | Nobody has forked it — red flag |

## Issue Resolution Rate

| Closed/total ratio | Score | Notes |
|----------------------|-------|-------|
| >80% | 10 | Excellent maintenance |
| 60–80% | 7 | Good maintenance |
| 40–60% | 4 | Irregular maintenance |
| <40% | 2 | Possible abandonment or overflow |
| 0 issues (total) | 5 | Neutral — could be new or very stable project |

## Last Commit

| Age | Score | Notes |
|------------|-------|-------|
| <7 days | 10 | Active development |
| 7–30 days | 8 | Recent activity |
| 30–90 days | 6 | Moderate activity |
| 90–180 days | 3 | Possible slowdown |
| 180–365 days | 1 | Probable abandonment |
| >365 days | 0 | Abandoned — override to maximum CAUTION |

## Contributors

| Quantity | Score | Notes |
|----------|-------|-------|
| >20 | 10 | Wide community, low bus factor |
| 10–20 | 8 | Healthy community |
| 5–10 | 6 | Small but viable team |
| 2–4 | 4 | Concerning bus factor |
| 1 | 1 | Bus factor = 1 — maximum abandonment risk |

**Bus factor = 1 override**: If the only contributor is also the only maintainer
and the repo has >100 stars, force CAUTION regardless of total score.

## Releases

| Status | Score | Notes |
|--------|-------|-------|
| SemVer + changelog + signed | 10 | Professional |
| SemVer + changelog | 8 | Best practice |
| Releases without SemVer | 5 | Functional but informal |
| Tags without releases | 3 | Minimal |
| No releases or tags | 1 | Signal of immature project |

## README

| Status | Score | Notes |
|--------|-------|-------|
| Complete: install, usage, API, examples, CI badges | 10 | |
| Good: install, basic usage, examples | 7 | |
| Basic: description + install | 4 | |
| Stub or auto-generated | 1 | |
| No README | 0 | Override: minimum CAUTION |

## CI/CD

| Status | Score | Notes |
|--------|-------|-------|
| Tests + lint + security scan + build matrix | 10 | |
| Tests + lint | 7 | |
| Tests only | 5 | |
| CI exists but only build | 3 | |
| No CI | 1 | |

## Phase 1 Calculation

```
P1 = average(stars, forks, issues, commit, contributors, releases, readme, ci)
```

The result is a value 0-10 multiplied by Phase 1 weight (0.20).
