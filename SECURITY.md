# Seguridad

InfoSails toma en serio la seguridad del harness y de los proyectos que instancia.

## Cómo reportar

**No** abras un issue público ni un PR que describa un exploit.

Opciones, en este orden:

1. [GitHub Security Advisory](https://github.com/infosails/infosails-harness/security/advisories/new) (privado).
2. Email: **jose@infosails.com** con asunto `SECURITY infosails-harness`.

Incluí:

- Descripción del problema y impacto (qué puede hacer un atacante).
- Pasos para reproducir o PoC **en privado**.
- Versión del kit (`config/harness.yaml` → `version`) y SO.
- Si afecta a proyectos instanciados (`infosails-init`), skills, hooks o scripts.

Confirmaremos recepción en unos días y te diremos si aplica un advisory y un parche.

## Alcance

En este repo importa sobre todo:

- Scripts (`install`, `init-project`, `update-project`, `pack`, `playground`, `record-usage`)
- Hook de tokens (`.cursor/hooks.json` → no debe filtrar secretos)
- Skills de Deploy (git push, Vercel) y Build (no inventar ni commitear secretos)
- Templates que se copian a proyectos (`.env.example`, rules)

Fuera de alcance típico (reportalo igual si no estás seguro):

- Apps de producto generadas por el harness (eso vive en otro git)
- Design systems de terceros que el producto elija (shadcn, MUI, `@infosails/design-system`, …)
- La factura o el dashboard de Cursor

## Secretos

Nunca commitees `.env`, `GITHUB_TOKEN`, `VERCEL_TOKEN` ni dumps de `playground/memory/costs` con datos reales de otra org.

Si publicás un secreto por error: rotarlo de inmediato y avisar por los canales de arriba.

## Versiones soportadas

Se parchea la rama `main` del kit. No hay LTS aparte. Actualizá proyectos con `infosails-update` tras un fix de seguridad.

## Divulgación

Preferimos coordinated disclosure. No pedimos embargo indefinido; sí tiempo razonable para parchear y avisar a quien use el kit.
