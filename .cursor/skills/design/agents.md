# Agentes internos — Design

| id | Rol |
|----|-----|
| `surveyor` | Landscape, as-built, **infra**, ADRs, BP |
| `architect` | Diseño app + **validar/proponer infra** + **topología de repos** |
| `scribe` | DS + updates landscape/as-built/infra/**repos**/ADRs; **crear repos** si aplica |

Orden: `surveyor` → `architect` → confirmación (app + infra + repos) → `scribe`.

No `complete` si el diseño asume infra o repos no listados/creados.
