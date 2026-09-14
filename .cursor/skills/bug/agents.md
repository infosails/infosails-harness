# Agentes internos — Bug

Solo el **Bug Lead** los conoce y activa (según el grafo).

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| id | Rol | Salida | kind |
|----|-----|--------|------|
| `triager` | Acotar el defecto con el usuario | Borrador del Bug Brief | human |
| `repro-scout` | Contexto en repo / tests relacionados | Notas al Lead | parallel (optional) |
| `critic` | Checklist del brief | pass/fail | gate |
| `scribe` | Persistir en memoria | `memory/bugs/*` | serial |

Frontier: `triager` ∥ `repro-scout` → `critic` → `scribe`.

Extensión futura (ej. `repro-runner`) = nodo nuevo en el grafo, sin cambiar el contrato outbox.
