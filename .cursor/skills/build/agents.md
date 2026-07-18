# Agentes internos — Build

Solo el **Build Lead** los conoce. La Orquesta no.

Guía de paralelismo / subagentes: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).  
SAST: [sast.md](sast.md).

| id | Rol | Paralelo |
|----|-----|----------|
| `surveyor` | Leer DS/BP + inventariar claves/accesos faltantes | no |
| `planner` | Work packages + grafo de dependencias | no |
| `tdd-dev` | Red→Green→Refactor por WP (seguridad de comportamiento en Red si aplica) | **sí** — **subagente por WP** |
| `coverage-gate` | Cobertura ≥ 85% | no (después de tdd) |
| `sast` | Análisis estático de seguridad (scope historia) | **sí** con mutation y e2e |
| `mutation` | Pruebas de mutación | **sí** con sast y e2e |
| `e2e` | Playwright + Gherkin; pedir BASE_URL/datos si faltan | **sí** con sast y mutation |
| `integrator` | Gates finales | no |
| `scribe` | Build Report + outbox | no |

## Flujo

```text
surveyor → planner → [tdd-dev ∥ tdd-dev …] → coverage-gate → [sast ∥ mutation ∥ e2e] → integrator → scribe
```

Cada caja `∥` = lanzar **subagentes en paralelo** (mismo turno). El Lead sintetiza y actualiza `state`.

Estándares: [standards.md](standards.md).
