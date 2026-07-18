# AGENTS.md — Proyecto InfoSails

**Orquesta → Lead de proceso → agentes internos (subagentes ∥)**

Cada Lead puede lanzar expertos como subagentes en paralelo  
(`.cursor/skills/_shared/lead-subagents.md`).

## Memoria

| Ruta | Rol |
|------|-----|
| `memory/director-state.json` | Orquesta |
| `memory/blueprints/` | Historias |
| `memory/bugs/` | Bugs |
| `memory/architecture/` | Landscape + ADRs |
| `memory/designs/` | Design Packages |
| `memory/builds/` | Build Reports |
| `memory/processes/` | Estado/inbox/outbox por Lead |

## Pipeline

Discovery → Design → Build → Deploy.  
Bugs: Bug → Build → Deploy.

Build: TDD, cobertura ≥85%, mutación, SAST, WPs en paralelo, Playwright + Gherkin.
