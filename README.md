# 🔍 GitHub Security Audit — A Claude Skill

> **Audit a GitHub repository for risk before integrating it into your stack.**
> Seven-dimension framework with weighted scoring and ADOPT / EVALUATE / CAUTION / AVOID verdict.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skill Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](CHANGELOG.md)
[![Languages](https://img.shields.io/badge/languages-EN%20%7C%20ES-green.svg)](#languages)
[![Inspired by](https://img.shields.io/badge/inspired%20by-Trail%20of%20Bits-orange.svg)](https://github.com/trailofbits)

🇬🇧 [English](#english) · 🇪🇸 [Español](#español)

---

## English

### What it does

When you ask Claude to audit a GitHub repo (e.g. *"audit repo https://github.com/owner/name"*), this skill drives a systematic, data-grounded risk evaluation across **seven dimensions** before recommending integration:

| # | Dimension | Weight |
|---|-----------|-------:|
| 1 | Project Health (stars, forks, commit cadence, contributors, releases, README, CI) | 20% |
| 2 | Code Security (secrets, dependencies, dynamic execution, insecure defaults, Actions) | 25% |
| 3 | Supply Chain (maintainer identity, funding, security history, signing, code review) | 20% |
| 4 | Code Quality (tests, types, docs, error handling, logging) | 10% |
| 5 | License & Legal Compatibility | 10% |
| 6 | MCP / Plugin / Skill Attack Surface (when applicable) | 15% |
| 7 | External Trust Signals (audits, CVEs, mentions) | bonus +5 |

The skill also enforces a **Step 0 Need Gate** — before auditing, you decide whether you actually need this repo. If your stack already covers the problem, the verdict is `SKIP`. No audit, no time wasted.

### Why it exists

Most AI-assisted "should I use this repo?" answers are vibes-based. This skill replaces vibes with:

- A fixed checklist (no skipping the unsexy stuff)
- Numeric scoring with documented thresholds
- **Override rules** that bypass the score (no license → automatic AVOID, regardless of how many stars)
- A list of *Rationalizations to Reject* (inspired by Trail of Bits): "it's open source so it's secure", "it has many stars", "company X uses it" — all flagged as insufficient evidence

### Installation

#### Claude.ai (Web)
1. Go to **Settings → Capabilities → Skills**
2. Click **+ New skill**
3. Upload the `en/github-security-audit/` folder (or `es/` for Spanish)

#### Claude Desktop
Same UI as Claude.ai, or drop the folder in:
```
~/Library/Application Support/Claude/skills/github-security-audit/   (macOS)
%APPDATA%\Claude\skills\github-security-audit\                       (Windows)
```

#### Claude Code (CLI)
```bash
mkdir -p ~/.claude/skills/github-security-audit
cp -r en/github-security-audit/* ~/.claude/skills/github-security-audit/
```

### Usage

Once installed, just ask Claude in natural language:

```
audit repo https://github.com/owner/repo-name
```

For a faster triage (only Phases 0+1+2):

```
audit repo quick https://github.com/owner/repo-name
```

The skill will produce an HTML report saved to your output directory, with:

- Executive summary (3–5 sentences)
- Radar chart of the 7 dimensions
- Per-phase findings table
- Critical issues (if any)
- Final verdict: **ADOPT 🟢 / EVALUATE 🟡 / CAUTION 🟠 / AVOID 🔴**
- Stack comparison: what existing tool covers similar functionality?

### Verdict scale

| Score | Verdict | Meaning |
|-------|---------|---------|
| ≥ 7.5 | **ADOPT** 🟢 | Integrate with confidence. Monitor updates. |
| 5.0–7.4 | **EVALUATE** 🟡 | Integrate with precautions. Document accepted risks. Re-review in 90 days. |
| 3.0–4.9 | **CAUTION** 🟠 | Only if no alternative. Isolate. Deep audit before production. |
| < 3.0 | **AVOID** 🔴 | Do not integrate. Find alternative. |

### Override rules (these bypass the numeric score)

- ❌ **No license** → automatic AVOID (default copyright = no permission to use)
- ❌ **AGPL / SSPL** → automatic AVOID for SaaS use
- ❌ **Hardcoded secrets in repo or git history** → automatic AVOID
- ⚠️ **Single maintainer + last commit > 1 year** → max CAUTION
- ❌ **Unsanitized dynamic execution in an MCP/plugin** → automatic AVOID

### Repository layout

```
.
├── en/                              ← English version of the skill
│   └── github-security-audit/
│       ├── SKILL.md                 ← orchestrator (what and when)
│       └── references/              ← detailed how-to (loaded on demand)
│           ├── health-scoring.md
│           ├── security-patterns.md
│           └── supply-chain-signals.md
├── es/                              ← Versión española del skill
│   └── github-security-audit/
│       └── (same structure)
├── examples/
│   └── example-report.md            ← sample audit output
├── README.md
├── LICENSE                          ← MIT
├── CONTRIBUTING.md
├── CHANGELOG.md
└── .gitignore
```

### Inspiration & credits

This skill draws heavily on the public security frameworks from [Trail of Bits](https://www.trailofbits.com/), specifically:

- `supply-chain-risk-auditor` — the systematic supply chain analysis framework
- `insecure-defaults` — the fail-secure vs fail-open principle
- `sharp-edges` — the "rationalizations to reject" list pattern
- `agentic-actions-auditor` — the MCP/plugin attack surface checklist

Built and maintained by [AMDT](https://evenmoredifficult.com/) — a digital marketing and SEO/AIO consultancy that uses Claude skills heavily for agency-level work.

### License

[MIT](LICENSE) — use it, fork it, ship it. Attribution appreciated, not required.

### Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Issues and PRs welcome, especially:
- New scoring patterns with citations
- Edge cases that broke the framework
- Translations into other languages
- Sharper thresholds backed by evidence

### Found a bug or have feedback?

Open an issue with the repo URL you were auditing and the verdict you expected vs got. Real-world failure cases are the most valuable input.

---

## Español

### Qué hace

Cuando le pides a Claude que audite un repo de GitHub (p. ej. *"audit repo https://github.com/owner/name"*), este skill conduce una evaluación de riesgo sistemática, basada en datos, a través de **siete dimensiones** antes de recomendar la integración:

| # | Dimensión | Peso |
|---|-----------|-----:|
| 1 | Salud del proyecto (stars, forks, cadencia de commits, contributors, releases, README, CI) | 20% |
| 2 | Seguridad del código (secrets, dependencias, ejecución dinámica, defaults inseguros, Actions) | 25% |
| 3 | Supply chain (identidad del maintainer, funding, historial de seguridad, firmado, code review) | 20% |
| 4 | Calidad del código (tests, tipado, docs, manejo de errores, logging) | 10% |
| 5 | Licencia y compatibilidad legal | 10% |
| 6 | Superficie de ataque MCP / Plugin / Skill (cuando aplica) | 15% |
| 7 | Señales de confianza externas (auditorías, CVEs, menciones) | bonus +5 |

El skill además impone un **Paso 0 — Gate de Necesidad**: antes de auditar, decides si realmente necesitas este repo. Si tu stack ya cubre el problema, el veredicto es `SKIP`. Sin auditoría, sin tiempo gastado.

### Por qué existe

La mayoría de respuestas asistidas por IA al "¿debería usar este repo?" se basan en intuición. Este skill sustituye la intuición por:

- Un checklist fijo (no se saltan los pasos aburridos)
- Scoring numérico con umbrales documentados
- **Reglas de override** que anulan el score (sin licencia → AVOID automático, sin importar cuántas stars)
- Una lista de *Racionalizaciones a Rechazar* (inspirada en Trail of Bits): "es open source así que es seguro", "tiene muchas stars", "lo usa la empresa X" — todas marcadas como evidencia insuficiente

### Instalación

#### Claude.ai (Web)
1. Ve a **Configuración → Capacidades → Skills**
2. Pulsa **+ Nuevo skill**
3. Sube la carpeta `es/github-security-audit/` (o `en/` para inglés)

#### Claude Desktop
Misma UI que Claude.ai, o copia la carpeta a:
```
~/Library/Application Support/Claude/skills/github-security-audit/   (macOS)
%APPDATA%\Claude\skills\github-security-audit\                       (Windows)
```

#### Claude Code (CLI)
```bash
mkdir -p ~/.claude/skills/github-security-audit
cp -r es/github-security-audit/* ~/.claude/skills/github-security-audit/
```

### Uso

Una vez instalado, pídele a Claude en lenguaje natural:

```
audit repo https://github.com/owner/repo-name
```

Para un descarte rápido (solo Fases 0+1+2):

```
audit repo quick https://github.com/owner/repo-name
```

El skill producirá un informe HTML guardado en tu directorio de salida, con:

- Resumen ejecutivo (3–5 frases)
- Radar chart de las 7 dimensiones
- Tabla de hallazgos por fase
- Issues críticos (si los hay)
- Veredicto final: **ADOPT 🟢 / EVALUATE 🟡 / CAUTION 🟠 / AVOID 🔴**
- Comparativa de stack: ¿qué herramienta existente cubre funcionalidad similar?

### Escala de veredicto

| Score | Veredicto | Significado |
|-------|-----------|-------------|
| ≥ 7,5 | **ADOPT** 🟢 | Integrar con confianza. Monitorizar actualizaciones. |
| 5,0–7,4 | **EVALUATE** 🟡 | Integrar con precauciones. Documentar riesgos aceptados. Revisar en 90 días. |
| 3,0–4,9 | **CAUTION** 🟠 | Solo si no hay alternativa. Aislar. Auditoría profunda antes de producción. |
| < 3,0 | **AVOID** 🔴 | No integrar. Buscar alternativa. |

### Reglas de override (anulan el score numérico)

- ❌ **Sin licencia** → AVOID automático (copyright por defecto = sin permiso de uso)
- ❌ **AGPL / SSPL** → AVOID automático para uso SaaS
- ❌ **Secrets hardcodeados en el repo o en el historial git** → AVOID automático
- ⚠️ **Único maintainer + último commit > 1 año** → CAUTION máximo
- ❌ **Ejecución dinámica no sanitizada en un MCP/plugin** → AVOID automático

### Inspiración y créditos

Este skill toma prestado de los frameworks públicos de seguridad de [Trail of Bits](https://www.trailofbits.com/), específicamente:

- `supply-chain-risk-auditor` — el framework sistemático de análisis de supply chain
- `insecure-defaults` — el principio fail-secure vs fail-open
- `sharp-edges` — el patrón de "racionalizaciones a rechazar"
- `agentic-actions-auditor` — el checklist de superficie de ataque para MCPs/plugins

Construido y mantenido por [AMDT)](https://aunmasdificiltodavia.es/) — consultoría de marketing digital y SEO/AIO que usa skills de Claude intensivamente para trabajo de agencia.

### Licencia

[MIT](LICENSE) — úsalo, forkea, distribuye. Atribución agradecida, no obligatoria.

### Contribuir

Ver [CONTRIBUTING.md](CONTRIBUTING.md). Issues y PRs bienvenidos, especialmente:
- Nuevos patrones de scoring con citas
- Casos límite que rompieron el framework
- Traducciones a otros idiomas
- Umbrales más afilados respaldados por evidencia

### ¿Has encontrado un bug o tienes feedback?

Abre un issue con la URL del repo que auditaste y el veredicto que esperabas vs el que obtuviste. Los casos de fallo del mundo real son el input más valioso.

---

<sub>Made with care · Issues and PRs welcome · Stars don't measure security, but they do help others find this</sub>
