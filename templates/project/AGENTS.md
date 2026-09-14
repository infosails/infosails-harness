# AGENTS.md — Proyecto InfoSails

**Orquesta → Lead de proceso → agentes internos (grafo / frontier ∥)**

Cada Lead ejecuta el **grafo** de su proceso (frontier → subagentes ∥).  
Guía: `.cursor/skills/_shared/lead-subagents.md` y `graph.md`.

## Memoria

| Ruta | Rol |
|------|-----|
| `memory/director-state.json` | Orquesta |
| `memory/blueprints/` | Historias |
| `memory/bugs/` | Bugs |
| `memory/architecture/` | Landscape + ADRs |
| `memory/designs/` | Design Packages |
| `memory/builds/` | Build Reports |
| `memory/onboard/` | Informes de seed (Onboard) |
| `memory/deploys/` | Deploy Reports (SHA en `main`) |
| `memory/lessons/` | Calidad rechazada aguas arriba (LSN-…) |
| `memory/trackers/` | Proyección Linear (opt-in; no es SoT) |
| `memory/costs/` | Tokens por historia (hook Cursor) |
| `memory/processes/` | Estado/inbox/outbox por Lead |

## Pipeline

Discovery → Design → Build → Deploy.  
Bugs: Bug → Build → Deploy.  
Brownfield: Onboard → (memoria as-is) → Discovery solo para lo nuevo.

Build: TDD, cobertura ≥85%, CCN≤10, mutación, SAST, WPs en paralelo, Playwright + Gherkin.  
Deploy: commit y push a `main` (producto + este home) y `vercel --prod` en las apps Vercel.
