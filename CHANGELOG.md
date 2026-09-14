# Changelog

Cambios notables del kit. El formato sigue [Keep a Changelog](https://keepachangelog.com/).
La versión canónica está en `config/harness.yaml`.

## [Unreleased]

- UI: el producto elige design system (landscape / `harness.project.yaml` / código / pregunta). `@infosails/design-system` es una opción, no un lock-in.
- Se elimina Linear (tracker, import, MCP, `memory/trackers/`, flags de `infosails-init`).
- README: guía de uso del ecosistema (quick start, frases, primera historia, troubleshooting) antes de la referencia del kit.

## [0.14.0] - 2026-09-14

Snapshot actual del kit al abrir el repositorio:

- Orquesta + Leads: Discovery, Bug, Design, Build, Onboard, Deploy
- Grafo de subagentes (frontier en paralelo), crítico de calidad y lecciones (`LSN-…`)
- Deploy: `git push` a `main` y `vercel --prod`; informes en `memory/deploys/`
- Costos de tokens por historia (`memory/costs/`, hook Cursor)
- Pack runtime (`./scripts/pack`) sin playground
- Licencia Apache 2.0 y archivos de comunidad (issues, seguridad, contribución)

Historial git anterior: ver commits en `main`.
