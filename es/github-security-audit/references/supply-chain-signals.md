# Supply Chain Signals — Framework de Evaluación

## 1. Identidad del Maintainer

### Señales VERDES

- Perfil público con historial >2 años
- Contribuciones a otros proyectos conocidos
- Presencia en conferencias/publicaciones
- Empleado de empresa conocida en el espacio
- Múltiples maintainers con historial independiente

### Señales ROJAS

- Cuenta GitHub creada recientemente (<6 meses)
- Sin foto, bio, ni enlaces
- Solo contribuciones a este repo
- Nombre genérico sin presencia online
- Patrón de actividad sospechoso (burst de commits y silencio)

### Scoring Identidad

| Señal | Score |
|-------|-------|
| Org conocida (FAANG, startup establecida, universidad) | 10 |
| Individuo con historial público verificable | 8 |
| Individuo con historial pero sin verificación externa | 5 |
| Perfil nuevo pero con contribuciones de calidad | 4 |
| Perfil anónimo o sin historial | 1 |

---

## 2. Organización y Funding

| Señal | Score |
|-------|-------|
| Empresa con modelo de negocio claro (open core, SaaS) | 10 |
| Fundación o colectivo con sponsors públicos | 9 |
| Proyecto de empresa con uso interno demostrado | 8 |
| Individuo con GitHub Sponsors / Open Collective | 6 |
| Proyecto personal sin funding | 3 |
| Origen desconocido | 1 |

---

## 3. Historial de Seguridad

### Qué buscar (vía web_search)

- `"<repo-name>" CVE` — ¿Tiene CVEs registrados?
- `"<repo-name>" vulnerability` — ¿Reportes de vulnerabilidad?
- `"<repo-name>" security advisory` — ¿GitHub Security Advisories?
- SECURITY.md en el repo — ¿Tiene política de disclosure?

### Scoring

| Señal | Score |
|-------|-------|
| SECURITY.md + historial de parches rápidos + advisories | 10 |
| SECURITY.md presente, sin incidentes conocidos | 8 |
| Sin SECURITY.md pero responde a issues de seguridad | 5 |
| CVEs abiertos sin parche | 2 |
| Vulnerabilidad reportada e ignorada | 0 |

---

## 4. Publicación y Distribución

### Registros oficiales

| Señal | Score |
|-------|-------|
| Verified publisher en npm/PyPI + 2FA + provenance | 10 |
| Publicado en registro oficial con usuario verificado | 8 |
| Publicado en registro oficial sin verificación | 5 |
| Solo instalable desde GitHub (no en registros) | 3 |
| Solo disponible como descarga directa/binario | 1 |

### Integridad de distribución

- ¿Lock file consistente con lo publicado?
- ¿Hay discrepancias entre código en GitHub y paquete publicado?
- ¿Usa GitHub Actions para publicar? (trazabilidad)
- ¿Tiene provenance attestation? (SLSA, Sigstore)

---

## 5. Code Review y Gobernanza

| Señal | Score |
|-------|-------|
| Branch protection + PR reviews obligatorios + CODEOWNERS | 10 |
| PR reviews en la mayoría de cambios | 7 |
| PRs existen pero sin review consistente | 4 |
| Push directo a main, sin PRs | 1 |

### Indicadores adicionales

- ¿Hay CONTRIBUTING.md con proceso definido?
- ¿Los PRs de externos reciben revisión?
- ¿Hay branch protection rules visibles?
- ¿Existe proceso de release documentado?

---

## 6. Firmado y Verificación

| Señal | Score |
|-------|-------|
| Commits firmados + releases firmadas + attestation | 10 |
| Releases firmadas / checksums | 7 |
| Commits firmados parcialmente | 4 |
| Sin firma de ningún tipo | 2 |

---

## 7. Resiliencia del Proyecto

### Bus Factor Analysis

```
Bus factor = número de personas que si desaparecen, el proyecto muere

Bus factor 1: UNA persona controla todo
  → Riesgo: enfermedad, burnout, abandono, account takeover
  → Mitigación: ¿hay forks activos? ¿documentación suficiente para continuidad?

Bus factor 2-3: Equipo pequeño
  → Riesgo moderado pero manejable
  → Verificar: ¿los otros contributors son activos recientemente?

Bus factor >5: Proyecto resiliente
  → Bajo riesgo de abandono
```

### Señales de abandono inminente

- Issues sin respuesta >90 días en aumento
- PRs de la comunidad sin review >60 días
- CI roto sin fix
- Dependabot PRs acumulados sin merge
- Último release >6 meses con issues abiertos

---

## Cálculo de Fase 3

```
F3 = promedio_ponderado(
  identidad × 0.20,
  organizacion × 0.15,
  historial_seguridad × 0.25,
  publicacion × 0.15,
  code_review × 0.15,
  firmado × 0.10
)
```

Aplicar override de bus factor si corresponde.
