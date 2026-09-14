# Contribuir

Gracias por querer mejorar el InfoSails Harness. Este repo es el **kit** (skills, templates, schemas, scripts). La memoria de un producto no vive aquí: vive en cada proyecto (`infosails-init`) o en `playground/` para pruebas locales.

Al enviar un PR aceptás que tu aporte se publica bajo la [Apache License 2.0](LICENSE), sin términos extra. El Código de conducta es [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).

## Antes de codear

1. Abrí un [issue](https://github.com/infosails/infosails-harness/issues) (bug, idea o duda) salvo que el cambio sea trivial (typo, docs).
2. Mantené el alcance chico: un tema por PR.
3. No subas secretos, `.env`, tokens, ni `playground/` (está gitignored a propósito).

## Requisitos

- [Cursor](https://cursor.com) (los agentes corren ahí)
- Git, bash, Python 3 (scripts y `./scripts/check`)
- macOS o Linux; zsh o bash

No hace falta Node para el kit. Los proyectos que genere el harness sí pueden pedirlo.

## Cómo trabajar en el kit

```bash
git clone https://github.com/infosails/infosails-harness.git
cd infosails-harness
./scripts/playground          # proyecto simulado en playground/
./scripts/check               # JSON, YAML mínimo, bash -n
```

En Cursor, con este workspace abierto: `hola` o `probar playground`.  
Guía de uso del ecosistema: [README](README.md) (sección *Usar el ecosistema*).  
`PROJECT_ROOT` pasa a ser `playground/` si existe `playground/harness.project.yaml`.

Otras órdenes útiles:

```bash
./scripts/playground --status
./scripts/pack                # runtime sin playground → dist/
./scripts/pack --no-archive
```

No commitees `dist/`, `playground/` (salvo `playground/README.md`) ni `.env`.

## Dónde cambia cada cosa

| Qué | Dónde |
|-----|--------|
| Lifecycle, leads, gates, política org | `config/harness.yaml` |
| Qué entra en el runtime | `config/pack.manifest` + `scripts/pack` |
| Skills de Orquesta / Leads | `.cursor/skills/` |
| Plantillas de artefacto y de proyecto | `templates/` |
| Contratos JSON | `schemas/` |
| Install / init / update | `scripts/` |
| Docs de fase (no van al pack) | `phases/` |

Subí la versión en `config/harness.yaml` solo cuando el cambio sea un release del kit (el maintainer suele hacerlo). Anotá el cambio en [CHANGELOG.md](CHANGELOG.md) bajo **Unreleased**.

## Convenciones

- Docs y skills en **español**, mismo tono que el README.
- No emojis en docs ni skills.
- Skills: el Lead habla con la Orquesta por inbox/outbox; la Orquesta no lanza agentes internos.
- No aflojes gates de Build (TDD, cov, CCN, mutación, SAST, e2e) sin discusión en el issue.
- El producto elige el **design system** (no hardcodees un paquete). `@infosails/design-system` es una opción, no el default.

## Pull requests

1. Branch desde `main`: `feat/…`, `fix/…`, `docs/…`.
2. `./scripts/check` en verde.
3. Si tocás pack/install/init/update, probá `./scripts/pack --no-archive` y, si aplica, `./scripts/playground`.
4. Completá la plantilla del PR: qué, por qué, cómo se probó.
5. Un PR no debe incluir `playground/memory`, lockfiles ajenos, ni reformateos masivos sin relación.

Los maintainers revisan jerarquía Orquesta ↔ Lead, schemas y que el pack no se hinche (el playground no va al runtime).

## Issues

Usá las plantillas de GitHub. En bugs: pasos, resultado esperado, resultado real, versión del kit (`config/harness.yaml`). No pegues tokens ni dumps de `.env`.

## Seguridad

Vulnerabilidades: [SECURITY.md](SECURITY.md). No las abras como issue público.

## Licencia y marca

El código es Apache 2.0. **InfoSails** es marca: podés atribuir el origen; no uses el nombre ni logos para insinuar que InfoSails respalda tu fork sin permiso. Detalle en [NOTICE](NOTICE).
