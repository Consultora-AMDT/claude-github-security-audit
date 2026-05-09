---
name: github-security-audit
description: >
  Auditoría de seguridad para repositorios GitHub antes de integrarlos en tu stack. 7 dimensiones:
  salud del proyecto, seguridad del código, supply chain, calidad, licencia, superficie MCP/plugin
  y señales de confianza externas. Scoring ponderado con veredicto ADOPT/EVALUATE/CAUTION/AVOID e
  informe HTML. Inspirado en los frameworks de Trail of Bits (supply-chain-risk-auditor,
  insecure-defaults). Actívalo con: auditoría repo, audit repo, seguridad repo, analizar repo,
  evaluar repo, evaluar librería, evaluar herramienta, review repo, supply chain, repo seguro,
  debería usar este repo, evaluar dependencia, es seguro este repo, security audit github,
  repo risk, evaluar MCP server, evaluar plugin, evaluar skill, evaluar integración. También
  cuando el usuario comparta un enlace a GitHub y pregunte si integrarlo. NO activar para
  análisis de código propio ni auditorías SEO.
---

# GitHub Security Audit — Evaluación de Riesgo para Repos

Auditoría sistemática de repositorios GitHub antes de integrarlos en un stack de producción
o recomendarlos a clientes. Inspirada en los frameworks de Trail of Bits
(supply-chain-risk-auditor, insecure-defaults, sharp-edges) pero adaptada a un flujo
agéntico MCP-first.

## Filosofía

```
NO evalúes repos con intuición. Evalúa con datos.
NO busques razones para adoptarlo. Busca razones para NO adoptarlo.
Si no encuentras razones para rechazarlo, entonces puede entrar.
```

**Regla de oro**: Un repo que resuelve un problema que tu stack ya cubre
NO aporta valor, independientemente de su calidad. Evaluamos primero
si hay necesidad real antes de gastar tiempo en el análisis completo.

---

## Dos modos de ejecución

| Modo | Comando | Bloques | Tool calls | Uso |
|------|---------|---------|------------|-----|
| **Completo** | `audit repo <url>` | 7 fases | 10-20 | Evaluación integral pre-integración |
| **Quick** | `audit repo quick <url>` | Fases 0+1+2 | 5-8 | Descarte rápido o primera impresión |

---

## PASO 0 — Gate de Necesidad (OBLIGATORIO, antes de CUALQUIER análisis)

Antes de analizar seguridad, responder:

1. **¿Qué problema resuelve este repo?** (definir en una frase)
2. **¿Está ese problema ya cubierto en el stack actual?** (MCPs, skills, herramientas)
3. **¿Aporta capacidad genuinamente nueva?**

Si la respuesta a (2) es SÍ y a (3) es NO → **VEREDICTO: SKIP** — no continuar.
Informar al usuario con la justificación y sugerir la herramienta existente.

Si hay duda razonable o el usuario insiste → continuar con Fase 1.

---

## FASE 1 — Salud del Proyecto

**Fuentes**: web_fetch del repo GitHub, GitHub API vía web_fetch.

Recopilar:

| Métrica | Cómo obtenerla | Umbral VERDE | Umbral ROJO |
|---------|---------------|--------------|-------------|
| Stars | Página del repo | >500 | <50 |
| Forks | Página del repo | >50 | <5 |
| Issues abiertos vs cerrados | Tabs del repo | Ratio cierre >60% | <30% |
| Último commit | Página de commits | <30 días | >180 días |
| Contributors | Tab contributors | >5 | 1 (bus factor) |
| Releases | Tab releases | Versionado semántico | Sin releases |
| README calidad | Contenido | Docs completas, ejemplos | Sin README o stub |
| CI/CD | GitHub Actions / badges | Tests automáticos | Sin CI |

**Scoring**: Cada métrica 0-10, peso total = 20% del scoring final.

Consultar `references/health-scoring.md` para tabla de puntuación detallada.

---

## FASE 2 — Seguridad del Código

**Fuentes**: web_fetch de archivos clave del repo.

### 2.1 Análisis estático (sin ejecutar código)

Revisar vía web_fetch:

- **Secrets hardcodeados**: buscar en código fuente patrones de API keys, tokens, passwords
  (`password =`, `api_key =`, `secret`, `token`, `.env` committeados)
- **Dependencias conocidas vulnerables**: revisar `package.json`, `requirements.txt`,
  `Cargo.toml`, `go.mod` — cruzar versiones con CVEs conocidos vía web_search
- **Permisos excesivos**: en GitHub Actions workflows (`.github/workflows/*.yml`),
  buscar `permissions: write-all`, `pull_request_target`, secrets en PRs de forks
- **Ejecución dinámica peligrosa**: `eval()`, `exec()`, `subprocess.call(shell=True)`,
  `dangerouslySetInnerHTML`, `innerHTML`, deserialization no segura
- **Configuración insegura por defecto**: siguiendo el patrón de Trail of Bits
  insecure-defaults — ¿el repo funciona de forma insegura si no configuras nada?

### 2.2 Dependencias transitivas

- ¿Cuántas dependencias directas tiene?
- ¿Hay dependencias con un solo maintainer?
- ¿Hay dependencias que hayan sido comprometidas anteriormente?

**Scoring**: 0-10, peso = 25% del scoring final.

Consultar `references/security-patterns.md` para checklist completo.

---

## FASE 3 — Supply Chain

**Fuentes**: web_search perfil del maintainer, historial del repo.

| Señal | VERDE | ROJO |
|-------|-------|------|
| Maintainer(s) identificables | Persona/org real, historial público | Cuenta nueva, sin historial |
| Organización respaldada | Empresa conocida, funding | Anónimo, sin contexto |
| Historial de seguridad | Vulnerabilidades parcheadas rápido | CVEs abiertos, sin respuesta |
| Publicación en registros oficiales | npm/PyPI con 2FA, verified publisher | Sin publicación oficial |
| Code review en PRs | PRs revisados antes de merge | Push directo a main |
| SECURITY.md | Existe con disclosure policy | No existe |
| Firmado de commits/releases | GPG signatures | Sin firmar |

**Scoring**: 0-10, peso = 20% del scoring final.

Consultar `references/supply-chain-signals.md` para análisis detallado.

---

## FASE 4 — Calidad de Código

**Fuentes**: web_fetch de archivos representativos.

- **Test coverage**: ¿Mencionado en README/CI? ¿Qué porcentaje?
- **Type hints / tipos estáticos**: ¿Usa tipado?
- **Documentación de API**: ¿Docstrings/JSDoc/rustdoc?
- **Arquitectura**: ¿Modular o monolítico? ¿Separación de concerns?
- **Error handling**: ¿Try/catch genéricos o manejo específico?
- **Logging**: ¿Tiene logging estructurado?

**Scoring**: 0-10, peso = 10% del scoring final.

---

## FASE 5 — Licencia y Compatibilidad Legal

**Fuentes**: web_fetch del archivo LICENSE.

| Licencia | Compatibilidad general | Notas |
|----------|---------------------|-------|
| MIT, BSD-2, BSD-3 | ✅ Total | Sin restricciones |
| Apache-2.0 | ✅ Total | Cláusula de patentes favorable |
| CC-BY-4.0, CC-BY-SA-4.0 | ⚠️ Parcial | OK para skills/docs, no para código |
| GPL-2.0, GPL-3.0 | ⚠️ Condicional | Copyleft — evaluar si es dependencia o integración |
| AGPL-3.0 | ❌ Alto riesgo | Copyleft de red — afecta SaaS |
| SSPL, BSL, propietaria | ❌ No usar | Restricciones comerciales |
| Sin licencia | ❌ No usar | Copyright por defecto, sin permisos |

**Scoring**: 0-10, peso = 10% del scoring final.

---

## FASE 6 — Superficie de Ataque MCP/Plugin/Skill

**Aplica cuando**: el repo es un MCP server, plugin de Claude Code, skill, herramienta
de integración, o cualquier software que se ejecute con acceso a datos de clientes.

Inspirado en Trail of Bits agentic-actions-auditor y sharp-edges.

### Checklist MCP/Plugin

- [ ] ¿Qué permisos pide? (filesystem, red, env vars, secrets)
- [ ] ¿Envía datos a terceros? (telemetría, analytics, APIs externas)
- [ ] ¿Almacena datos localmente? ¿Dónde? ¿Cifrado?
- [ ] ¿Tiene hooks que se ejecuten automáticamente? (pre/post tool use)
- [ ] ¿El código del MCP server es auditable? ¿O es un binario/servicio remoto?
- [ ] ¿Usa `eval()`, `exec()` o ejecución dinámica de código proporcionado por el usuario?
- [ ] ¿Valida inputs del LLM antes de ejecutar? (prompt injection surface)
- [ ] ¿Tiene rate limiting?
- [ ] ¿Qué pasa si falla? (fail-open vs fail-secure)

### Racionalizaciones a Rechazar

(Inspirado en Trail of Bits "Rationalizations to Reject")

- "Es open source, así que es seguro" → NO. Open source ≠ auditado.
- "Tiene muchas stars" → NO. Stars miden popularidad, no seguridad.
- "Lo usa empresa X" → NO. A menos que empresa X haya hecho auditoría pública.
- "Es solo lectura" → VERIFICAR. ¿Realmente? ¿No escribe logs, caché, temp files?
- "Solo lo usaré internamente" → NO reduce el riesgo si toca datos de clientes.
- "El maintainer parece confiable" → VERIFICAR con datos, no con sensación.

**Scoring**: 0-10, peso = 15% del scoring final (0% si no aplica, redistribuido).

---

## FASE 7 — Señales de Confianza Externas

**Fuentes**: web_search.

- ¿Ha sido mencionado en publicaciones de seguridad? (positiva o negativamente)
- ¿Tiene auditorías de terceros?
- ¿Aparece en listas curadas de calidad? (awesome-X, Trail of Bits skills-curated)
- ¿Ha sido reportado como malicioso en algún momento?
- ¿Tiene programa de bug bounty o SECURITY.md?

**Scoring**: 0-10, peso = bonus (no resta, solo suma hasta +5 puntos al total).

---

## Cálculo de Scoring Final

```
SCORE = (F1 × 0.20) + (F2 × 0.25) + (F3 × 0.20) + (F4 × 0.10) + (F5 × 0.10) + (F6 × 0.15) + bonus_F7

Si F6 no aplica:
SCORE = (F1 × 0.24) + (F2 × 0.29) + (F3 × 0.23) + (F4 × 0.12) + (F5 × 0.12) + bonus_F7
```

### Veredicto

| Score | Veredicto | Acción |
|-------|-----------|--------|
| ≥7.5 | **ADOPT** 🟢 | Integrar con confianza. Monitorizar actualizaciones. |
| 5.0–7.4 | **EVALUATE** 🟡 | Integrar con precauciones. Documentar riesgos aceptados. Revisar en 90 días. |
| 3.0–4.9 | **CAUTION** 🟠 | Solo si no hay alternativa. Aislar. Auditoría profunda antes de producción. |
| <3.0 | **AVOID** 🔴 | No integrar. Buscar alternativa. |

### Override rules (anulan el scoring numérico)

- Sin licencia → **AVOID** automático
- AGPL/SSPL → **AVOID** automático (salvo uso interno sin SaaS)
- Secrets hardcodeados en el repo → **AVOID** automático
- Único maintainer + último commit >1 año → **CAUTION** máximo
- Ejecución dinámica no sanitizada en MCP → **AVOID** automático

---

## Entregable

Generar un informe HTML interactivo con:

1. **Header**: Nombre del repo, URL, fecha, veredicto con badge de color
2. **Resumen ejecutivo**: 3-5 frases con hallazgos clave
3. **Radar chart**: Las 7 dimensiones en gráfico radial (o 6 si F6 no aplica)
4. **Detalle por fase**: Tabla con métricas, valores encontrados, scoring
5. **Hallazgos críticos**: Lista de issues que requieren atención (si los hay)
6. **Racionalizaciones rechazadas**: Si durante el análisis se detectaron
7. **Veredicto final**: Score + veredicto + acción recomendada
8. **Comparativa stack**: ¿Qué herramienta actual cubre funcionalidad similar?

Guardar en el directorio de trabajo/output.

---

## Cuándo usar

- Antes de integrar cualquier repo/librería/MCP/plugin/skill al stack
- Cuando un cliente pregunte sobre la seguridad de una herramienta
- Cuando evalúes una dependencia nueva para un proyecto
- Cuando el usuario comparta un enlace a GitHub preguntando si usarlo
- Para auditar MCPs o skills de terceros antes de instalarlos

## Cuándo NO usar

- Para auditorías SEO de sitios web
- Para análisis de código propio (usar tu workflow de dev/test)
- Para crear skills nuevos (usar skill-creator)
- Para evaluar herramientas que no son open source / no tienen repo público

---

## Referencias

Consultar bajo demanda:

- `references/health-scoring.md` — Tabla detallada de scoring por métrica de salud
- `references/security-patterns.md` — Checklist completo de patrones de seguridad
- `references/supply-chain-signals.md` — Framework de evaluación de supply chain
