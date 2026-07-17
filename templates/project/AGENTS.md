# AGENTS.md — Proyecto InfoSails

**Orquesta → Lead de proceso → agentes internos**

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

Build: TDD, cobertura ≥85%, mutación, WPs en paralelo, e2e si aplica.
