# Agentes internos — Design

Solo el **Design Lead** los conoce y activa (según el grafo).

Grafo: [graph.yaml](graph.yaml). Guía: [../_shared/lead-subagents.md](../_shared/lead-subagents.md).

| id | Rol | kind |
|----|-----|------|
| `surveyor` | Landscape, as-built, infra, ADRs, BP, **lecciones** | serial |
| `architect` | Diseño app + infra + repos + Mermaid | parallel |
| `ui-designer` | UI/UX + kit del producto (elegir / heredar); skip si no hay UI | parallel (optional) |
| `critic` | Hexagonal / BP / paths / Gherkin mapeable / UI mapa | gate |
| `scribe` | DS + landscape/as-built/infra/repos/ADRs; crear repos si aplica | serial |

`surveyor` → [`architect` ∥ `ui-designer`] → `critic` → confirmación humana del Lead → `scribe`.

Si faltan perfiles o tipo de app en el BP: no inventar. `blocked` + lección a Discovery.

Guía UI: [ui-designer.md](ui-designer.md). Diagramas: [diagrams.md](diagrams.md). Crítico: [../_shared/critic.md](../_shared/critic.md).
