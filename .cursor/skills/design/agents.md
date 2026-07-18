# Agentes internos — Design

Solo el **Design Lead** los conoce y activa (como **subagentes** en paralelo cuando aplique).

Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| id | Rol | Paralelo |
|----|-----|----------|
| `surveyor` | Landscape, as-built, **infra**, ADRs, BP | no (primero) |
| `architect` | Diseño app + infra + repos + **Mermaid** (secuencia/componentes) | **sí** con ui-designer |
| `ui-designer` | UI/UX + perfiles/tipo app del BP + sabores **`@infosails/design-system`**; skip si no hay UI | **sí** con architect |
| `scribe` | DS + updates landscape/as-built/infra/**repos**/ADRs; **crear repos** si aplica | no |

Orden: `surveyor` → [`architect` ∥ `ui-designer`] → confirmación (app + infra + repos + UI) → `scribe`.

Guía UI: [ui-designer.md](ui-designer.md).  
Diagramas: [diagrams.md](diagrams.md).

No `complete` si el diseño asume infra o repos no listados/creados, o UI sin mapa al design system (cuando hay pantallas).
