# Contributing

Thanks for considering a contribution. This skill is intentionally small and focused — it does one thing (audit a GitHub repo for risk before integration) and tries to do it well.

## English

### What kinds of contributions are welcome

- **New scoring patterns** — additional security/supply-chain signals not yet covered, with citations
- **Edge cases** — repos that broke the framework or produced misleading verdicts
- **Translations** — versions of the skill in other languages (kept in sibling top-level folders: `de/`, `fr/`, `pt/`, etc.)
- **Reference improvements** — sharper thresholds, clearer scoring tables, better examples
- **Bug fixes** — typos, broken links, factual errors

### What is out of scope

- Adding new phases beyond the seven existing ones (the framework is deliberately bounded)
- Tying the skill to a specific MCP stack or vendor
- Adding code execution or auto-remediation (this skill is read-only by design)

### Process

1. Open an issue first describing the change. For new scoring patterns or thresholds, please cite a source (Trail of Bits report, CVE, postmortem, academic paper).
2. Fork → branch → PR. Keep PRs focused: one change per PR.
3. Update both the English and Spanish versions if your change affects the SKILL.md or references. If you can't translate, that's fine — open the PR in one language and flag it; a maintainer or another contributor will help with the other.
4. Update `CHANGELOG.md` under an `## [Unreleased]` section.

### Style guide

- SKILL.md should stay focused on orchestration ("what and when"). Detailed how-to belongs in `references/`.
- Keep the `description:` frontmatter under 1024 characters.
- Tables over prose when listing thresholds, scores, or signals.
- Cite sources for security claims.

### Code of conduct

Be specific, be kind, attack ideas not people. Bad-faith contributions (typosquatting attempts, security signal noise designed to lower thresholds, etc.) will be closed without discussion.

---

## Español

### Qué contribuciones son bienvenidas

- **Nuevos patrones de scoring** — señales adicionales de seguridad/supply-chain no cubiertas aún, con citas
- **Casos límite** — repos que rompieron el framework o produjeron veredictos engañosos
- **Traducciones** — versiones del skill en otros idiomas (en carpetas hermanas a nivel raíz: `de/`, `fr/`, `pt/`, etc.)
- **Mejoras a las referencias** — umbrales más afilados, tablas de scoring más claras, mejores ejemplos
- **Correcciones** — erratas, enlaces rotos, errores factuales

### Qué está fuera del alcance

- Añadir fases nuevas más allá de las siete existentes (el framework está deliberadamente acotado)
- Atar el skill a un stack MCP específico o a un vendor concreto
- Añadir ejecución de código o auto-remediación (este skill es read-only por diseño)

### Proceso

1. Abre primero un issue describiendo el cambio. Para nuevos patrones o umbrales, cita una fuente (informe de Trail of Bits, CVE, post-mortem, paper).
2. Fork → branch → PR. Mantén los PRs enfocados: un cambio por PR.
3. Actualiza tanto la versión inglesa como la española si tu cambio afecta al SKILL.md o a las referencias. Si no puedes traducir, no pasa nada — abre el PR en un idioma y márcalo; un maintainer u otro contributor ayudará con el otro.
4. Actualiza `CHANGELOG.md` bajo una sección `## [Unreleased]`.

### Guía de estilo

- SKILL.md debe centrarse en orquestación ("qué y cuándo"). El how-to detallado va en `references/`.
- Mantén el campo `description:` del frontmatter bajo 1024 caracteres.
- Tablas en lugar de prosa al listar umbrales, scores o señales.
- Cita las fuentes en afirmaciones de seguridad.

### Código de conducta

Sé específico, sé amable, ataca ideas no personas. Las contribuciones de mala fe (intentos de typosquatting, ruido en señales de seguridad diseñado para bajar umbrales, etc.) se cerrarán sin discusión.
