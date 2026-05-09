# Health Scoring — Tabla de Puntuación Detallada

## Stars

| Rango | Score | Notas |
|-------|-------|-------|
| >10.000 | 10 | Proyecto establecido, amplia adopción |
| 5.000–10.000 | 9 | Muy popular, comunidad activa |
| 1.000–5.000 | 8 | Popular, tracción demostrada |
| 500–1.000 | 7 | Buena adopción |
| 200–500 | 5 | Adopción moderada |
| 50–200 | 3 | Proyecto nicho o emergente |
| <50 | 1 | Proyecto nuevo o desconocido |

**Nota**: Stars por sí solas NO son indicador de seguridad ni calidad. Son indicador de visibilidad.

## Forks

| Rango | Score | Notas |
|-------|-------|-------|
| >500 | 10 | Extensamente bifurcado, señal de uso real |
| 100–500 | 8 | Uso significativo |
| 50–100 | 6 | Uso moderado |
| 10–50 | 4 | Uso limitado |
| <10 | 2 | Uso mínimo |
| 0 | 0 | Nadie lo ha bifurcado — señal de alarma |

## Issue Resolution Rate

| Ratio cerrados/total | Score | Notas |
|----------------------|-------|-------|
| >80% | 10 | Mantenimiento excelente |
| 60–80% | 7 | Buen mantenimiento |
| 40–60% | 4 | Mantenimiento irregular |
| <40% | 2 | Posible abandono o desbordamiento |
| 0 issues (total) | 5 | Neutral — puede ser proyecto nuevo o muy estable |

## Último Commit

| Antigüedad | Score | Notas |
|------------|-------|-------|
| <7 días | 10 | Desarrollo activo |
| 7–30 días | 8 | Actividad reciente |
| 30–90 días | 6 | Actividad moderada |
| 90–180 días | 3 | Posible desaceleración |
| 180–365 días | 1 | Probable abandono |
| >365 días | 0 | Abandonado — override a CAUTION máximo |

## Contributors

| Cantidad | Score | Notas |
|----------|-------|-------|
| >20 | 10 | Comunidad amplia, bajo bus factor |
| 10–20 | 8 | Comunidad saludable |
| 5–10 | 6 | Equipo pequeño pero viable |
| 2–4 | 4 | Bus factor preocupante |
| 1 | 1 | Bus factor = 1 — riesgo máximo de abandono |

**Bus factor = 1 override**: Si el único contributor es también el único maintainer
y el repo tiene >100 stars, forzar CAUTION independientemente del score total.

## Releases

| Estado | Score | Notas |
|--------|-------|-------|
| SemVer + changelog + firmadas | 10 | Profesional |
| SemVer + changelog | 8 | Buena práctica |
| Releases sin SemVer | 5 | Funcional pero informal |
| Tags sin releases | 3 | Mínimo |
| Sin releases ni tags | 1 | Señal de proyecto inmaduro |

## README

| Estado | Score | Notas |
|--------|-------|-------|
| Completo: install, uso, API, ejemplos, badges CI | 10 | |
| Bueno: install, uso básico, ejemplos | 7 | |
| Básico: descripción + install | 4 | |
| Stub o auto-generado | 1 | |
| Sin README | 0 | Override: CAUTION mínimo |

## CI/CD

| Estado | Score | Notas |
|--------|-------|-------|
| Tests + lint + security scan + build matrix | 10 | |
| Tests + lint | 7 | |
| Solo tests | 5 | |
| CI existe pero solo build | 3 | |
| Sin CI | 1 | |

## Cálculo de Fase 1

```
F1 = promedio(stars, forks, issues, commit, contributors, releases, readme, ci)
```

El resultado es un valor 0-10 que se multiplica por el peso de Fase 1 (0.20).
