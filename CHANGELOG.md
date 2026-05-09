# Changelog

All notable changes to this skill will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-05-09

### Added

- Initial public release
- Seven-phase audit framework (Health, Code Security, Supply Chain, Quality, License, MCP/Plugin Surface, External Trust)
- Two execution modes: full audit (`audit repo <url>`) and quick triage (`audit repo quick <url>`)
- Mandatory Step 0 "Need Gate" — evaluate whether a repo is actually needed before auditing it
- Weighted scoring with ADOPT/EVALUATE/CAUTION/AVOID verdict
- Override rules for automatic AVOID (no license, AGPL/SSPL for SaaS, hardcoded secrets, unsanitized dynamic execution in MCPs)
- Bus factor analysis for project resilience
- Three reference files loaded on demand:
  - `references/health-scoring.md`
  - `references/security-patterns.md`
  - `references/supply-chain-signals.md`
- HTML report deliverable specification with radar chart over the seven dimensions
- Bilingual distribution: `en/` and `es/` versions of the skill

### Inspired by

- Trail of Bits — `supply-chain-risk-auditor`, `insecure-defaults`, `sharp-edges`, `agentic-actions-auditor` skills
