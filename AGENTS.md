# AGENTS.md — InfoSails Harness (kit)

## Jerarquía

Usuario ↔ Orquesta ↔ Lead ↔ agentes internos (**grafo**; frontier en paralelo).

Guía: `.cursor/skills/_shared/lead-subagents.md` (grafo + frontier)

## Modo playground

Si existe `playground/harness.project.yaml` → `PROJECT_ROOT = playground/`.

## Skills

| Skill | Rol |
|-------|-----|
| director | Orquesta (Linear opt-in: `linear.md`; costos: `costs.md`) |
| discovery | Discovery Lead |
| bug | Bug Lead |
| design | Design Lead (hexagonal + Mermaid + ui-designer / `@infosails/design-system`) |
| build | Build Lead (TDD, cov≥85%, CCN≤10, mutación, SAST, Playwright+Gherkin) |
| onboard | Onboard Lead (hidratar memoria desde proyecto ya iniciado) |
| deploy | Deploy Lead (git `main` + `vercel --prod`; home versiona `memory/`) |

Grafo de ejecución y crítico: `_shared/graph.md`, `critic.md`, `lessons.md`.
