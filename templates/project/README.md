# {{PROJECT_NAME}}

Proyecto agentico instanciado con [InfoSails Harness]({{HARNESS_HOME}}).

## Memoria

Lo que hay que construir/corregir vive en `memory/` (resultado de Discovery/Bug):

- `memory/blueprints/` — historias (Feature Blueprints)
- `memory/bugs/` — defectos
- `memory/backlog.json` — cola
- `memory/trackers/` — Linear (opt-in; proyección, no fuente de verdad)
- `memory/costs/` — tokens por historia (hook Cursor `stop`)
- `memory/deploys/` — informes de land en `main`
- `memory/lessons/` — lecciones de calidad entre procesos
- `memory/director-state.json` — estado de procesos (solo el Director)

El Director activa fases y delega (Discovery → Design → Build → Deploy). Deploy pushea `main` y publica con `vercel --prod`.

## Cómo trabajar

1. Abre este repo en Cursor.
2. Escribe `hola` / `director` → el Director lee el estado y pregunta qué hacer.
3. Discovery guarda historias en `memory/blueprints/`; el Director propone la siguiente fase.

El Director activa el **Discovery Lead**; el Lead informa por outbox cuando termina. No uses el repo del kit para la memoria de este producto.
