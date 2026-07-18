# AGENTS.md — InfoSails Harness (kit)

## Jerarquía

Usuario ↔ Orquesta ↔ Lead ↔ agentes internos (**subagentes**, paralelo si independientes).

Guía: `.cursor/skills/_shared/lead-subagents.md`

## Modo playground

Si existe `playground/harness.project.yaml` → `PROJECT_ROOT = playground/`.

## Skills

| Skill | Rol |
|-------|-----|
| director | Orquesta |
| discovery | Discovery Lead |
| bug | Bug Lead |
| design | Design Lead (hexagonal + Mermaid + ui-designer / `@infosails/design-system`) |
| build | Build Lead (TDD, cov≥85%, mutación, SAST, Playwright+Gherkin) |
