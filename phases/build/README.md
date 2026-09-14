# Fase: Build

**Status:** active  
**Lead:** Build Lead  
**Skill:** `.cursor/skills/build/SKILL.md`  
**Input:** `memory/designs/` (+ blueprint/bug)  
**Output:** código del producto + `memory/builds/` (Build Report)

## Estándares

Ver `.cursor/skills/build/standards.md`:

- TDD (Red → Green → Refactor; seguridad de comportamiento en Red si aplica)
- Cobertura **≥ 85%**
- Complejidad ciclomática **CCN ≤ 10** (funciones nuevas/tocadas; agente `complexity-gate`)
- Pruebas de **mutación**
- **SAST** (análisis estático de seguridad; agente `sast`)
- Work packages **en paralelo** cuando se pueda
- **E2E** con **Playwright**, escenarios del blueprint en **Gherkin**
- Garantizar (o **pedir**) lo necesario para correr pruebas: BASE_URL, datos, browsers
- Si faltan **claves/secretos/accesos** → pedirlos al usuario (no inventar)

## Agentes internos

`surveyor` → `planner` → `[tdd-dev ∥ …]` → `coverage-gate` → `[sast ∥ mutation ∥ e2e ∥ complexity-gate]` → `integrator` → `critic` → `scribe`

Cada `∥` = **subagentes en paralelo** (`.cursor/skills/_shared/lead-subagents.md`).  
SAST: `.cursor/skills/build/sast.md`. Complejidad: `.cursor/skills/build/complexity.md`.
