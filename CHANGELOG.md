# Changelog

Cambios notables del kit. El formato sigue [Keep a Changelog](https://keepachangelog.com/).
La versión canónica está en `config/harness.yaml`.

## [Unreleased]

- Deploy en dos fases: `vercel deploy` sin `--prod` (dev) y, para prod, tag semver (`vX.Y.Z`, bump patch por defecto) antes de `vercel --prod`.
- Tras el deploy de dev, alias al host de producción con `-dev` al final de la primera etiqueta (`acme.vercel.app` → `acme-dev.vercel.app`).
- Prod (tag semver y `vercel --prod`) pide autorización después de la URL de dev. El pedido inicial de deploy no alcanza.
- Antes de git y de Vercel, Deploy pregunta si es dev y prod o solo prod. Dev se salta cuando el proyecto solo tiene producción.
- UI: el producto elige design system (landscape / `harness.project.yaml` / código / pregunta). `@infosails/design-system` es una opción, no un lock-in.
- Se elimina Linear (tracker, import, MCP, `memory/trackers/`, flags de `infosails-init`).
- README: guía de uso del ecosistema (quick start, frases, primera historia, troubleshooting) antes de la referencia del kit.
- Textos del kit en español neutro (tú; sin voseo rioplatense).

## [0.14.0] - 2026-09-14

Snapshot actual del kit al abrir el repositorio:

- Orquesta + Leads: Discovery, Bug, Design, Build, Onboard, Deploy
- Grafo de subagentes (frontier en paralelo), crítico de calidad y lecciones (`LSN-…`)
- Deploy: `git push` a `main` y `vercel --prod`; informes en `memory/deploys/`
- Costos de tokens por historia (`memory/costs/`, hook Cursor)
- Pack runtime (`./scripts/pack`) sin playground
- Licencia Apache 2.0 y archivos de comunidad (issues, seguridad, contribución)

Historial git anterior: ver commits en `main`.
