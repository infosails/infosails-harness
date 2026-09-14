# [BR-ID] Build Report: [Nombre corto]

> **Design Package:** [DS-ID]
> **Blueprint / Bug:** [BP-ID | BUG-ID]
> **Status:** Ready for Deploy | Blocked
> **Creado por:** Build Lead
> **Estándares:** TDD · cobertura ≥85% · mutación · SAST · Playwright + Gherkin

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
| ID | Descripción | depends_on | writes | Estado |
|----|-------------|------------|--------|--------|
| WP-01 | | | | done |

## 3. TDD (memoria = test en el diff, no el % de cobertura)
| WP | test_files (mismo diff) | red_first | business_asserts (Gherkin/Core) | Crítico WP |
|----|-------------------------|-----------|----------------------------------|------------|
| WP-01 | | sí/no | | pass / fail |

* Mutación (más abajo) debe matar mutantes de esos asserts. Coverage solo no cierra esta sección.

## 4. Cobertura
| Scope | Métrica | Resultado | Gate (≥85%) |
|-------|---------|-----------|-------------|
| Historia / diff | lines (o statements) | % | PASS / FAIL |
| | branches (si aplica) | % | PASS / FAIL / n/a |

Comando(s):

```bash
```

## 4b. Complejidad ciclomática
| Scope | max CCN | Umbral | Gate |
|-------|---------|--------|------|
| Funciones nuevas/tocadas | | ≤10 | PASS / FAIL |

Herramienta + comando:

```bash
```

Ofensores (path, símbolo, CCN):

| Path | Símbolo | CCN |
|------|---------|-----|
| | | |

## 5. Mutación
| Scope | Score | Umbral | Gate |
|-------|-------|--------|------|
| | % | ≥70% (default) | PASS / FAIL |

Herramienta + comando:

```bash
```

## 5b. SAST (análisis estático de seguridad)
* Herramienta: Semgrep | Bandit | otro:
* Scope (paths):
* Ejecutado: sí / blocked
* Gate (ERROR/HIGH/CRITICAL in-scope): PASS / FAIL
* Comando:

```bash
# ej. semgrep --config auto --error <paths>
```

| Hallazgo | Severidad | Path | In-scope | Acción |
|----------|-----------|------|----------|--------|
| | | | sí / preexistente | fix / aceptar / n/a |

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
* [ ] TDD (incl. seguridad de comportamiento si aplica)
* [ ] Cobertura ≥ 85%
* [ ] Complejidad ciclomática (CCN ≤ 10 nuevas/tocadas)
* [ ] Mutación
* [ ] SAST (sin bloqueantes in-scope)
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
# complexity (lizard --CCN 10 …)
# sast
# e2e
```
