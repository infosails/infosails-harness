# [BR-ID] Build Report: [Nombre corto]

> **Design Package:** [DS-ID]
> **Blueprint / Bug:** [BP-ID | BUG-ID]
> **Status:** Ready for Deploy | Blocked
> **Creado por:** Build Lead
> **Estándares:** TDD · cobertura ≥85% · mutación · Playwright + Gherkin

## 1. Resumen
* 

## 1b. Prerrequisitos (claves / secretos / acceso / capacidad de probar)
| Ítem | ¿Presente? | Cómo se resolvió |
|------|------------|------------------|
| | sí / pedido / n/a | env / usuario / mock autorizado |
| Playwright + browsers | | |
| BASE_URL / app alcanzable | | |
| Datos / usuarios Gherkin | | |

* Pedidos al usuario en esta corrida:
* 

## 2. Work packages
| ID | Descripción | Paralelo | Estado |
|----|-------------|----------|--------|
| WP-01 | | yes/no | done |

## 3. TDD
* Red → Green → Refactor por WP: sí / no
* Notas:

## 4. Cobertura
| Scope | Métrica | Resultado | Gate (≥85%) |
|-------|---------|-----------|-------------|
| Historia / diff | lines (o statements) | % | PASS / FAIL |
| | branches (si aplica) | % | PASS / FAIL / n/a |

Comando(s):

```bash
```

## 5. Mutación
| Scope | Score | Umbral | Gate |
|-------|-------|--------|------|
| | % | ≥70% (default) | PASS / FAIL |

Herramienta + comando:

```bash
```

## 6. E2E (Playwright + Gherkin)
* Herramienta: **Playwright**
* Ejecutado: sí / skipped
* Razón si skipped:
* Resultado suite:
* Comando:

```bash
# ej. pnpm test:e2e / npx playwright test
```

### Mapeo Gherkin → Playwright
| Escenario (BP) | Spec / test | Resultado |
|----------------|-------------|-----------|
| Escenario 1: … | e2e/….spec.ts | PASS / FAIL |
| Escenario 2: … | | |
| Escenario 3: … | | |

## 7. Gates
* [ ] Prerrequisitos (claves/accesos)
* [ ] Capacidad de probar (Playwright + URL/datos)
* [ ] TDD
* [ ] Cobertura ≥ 85%
* [ ] Mutación
* [ ] E2E Playwright + Gherkin (o skip justificado)
* [ ] Guardrails DS
* [ ] Todos los escenarios Gherkin PASS (o N/A)

## 8. Cambios principales
| Path | Cambio |
|------|--------|
| | |

## 9. Infra tocada (Vercel / GCP)
* 

## 10. Cómo reproducir verificación

```bash
# tests
# coverage
# mutation
# e2e
```
