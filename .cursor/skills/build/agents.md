# Agentes internos — Build

Solo el **Build Lead** los conoce. La Orquesta no.

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).  
SAST: [sast.md](sast.md). Complejidad: [complexity.md](complexity.md). Crítico: [../_shared/critic.md](../_shared/critic.md).

| id | Rol | kind |
|----|-----|------|
| `surveyor` | Leer DS/BP + lecciones + prerrequisitos | serial |
| `planner` | WPs con `depends_on` + `writes`; instanciar `tdd-dev:*` | serial |
| `tdd-dev` | Red→Green→Refactor por WP (un nodo por WP) | parallel (instancias) |
| `coverage-gate` | Cobertura ≥ 85% | gate |
| `complexity-gate` | CCN McCabe ≤ 10 en funciones nuevas/tocadas | parallel |
| `sast` | SAST estático (scope historia) | parallel |
| `mutation` | Mutación; score ≥ umbral | parallel |
| `e2e` | Playwright + Gherkin | parallel (optional skip) |
| `integrator` | Gates finales | gate |
| `critic` | TDD real + gates; DS hueco → lección a Design | gate |
| `scribe` | Build Report + outbox | serial |

## Flujo

```text
surveyor → planner → [tdd-dev:* según depends_on/writes] → coverage-gate
  → [sast ∥ mutation ∥ e2e ∥ complexity-gate] → integrator → critic → scribe
```

The `planner` instancía `tdd-dev:*`. El Lead **no** marca un WP `done` sin crítico de diff ([../_shared/critic.md](../_shared/critic.md) § Por WP).

Estándares: [standards.md](standards.md).
