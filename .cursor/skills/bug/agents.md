# Agentes internos — Bug

Solo el **Bug Lead** los conoce y activa (como **subagentes** cuando aplique).

Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| id | Rol | Salida | Paralelo |
|----|-----|--------|----------|
| `triager` | Acotar el defecto con el usuario | Borrador del Bug Brief | no (usuario) |
| `repro-scout` (opcional) | Contexto en repo / tests relacionados | Notas al Lead | **sí** |
| `scribe` | Persistir en memoria | `memory/bugs/*` | no |

Extensión futura (ej. `repro-runner`) sin cambiar el contrato outbox con la Orquesta.
