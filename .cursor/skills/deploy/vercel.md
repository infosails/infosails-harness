# Vercel — dev y después producción

El Deploy Lead publica en **dos fases**, después de pushear `main` en el git de producto.

1. **Dev** — solo si el usuario eligió dev y prod. Deployment sin `--prod` (preview de Vercel).
2. **Prod** — solo después de la autorización y del tag semver en `origin` ([git.md](git.md)): `vercel --prod`. Si eligió solo prod, no hay fase dev.

Solo aplica a deployables en **Vercel** (landscape / DS / `.vercel`). El **home** (`memory/`) no se publica. Backends solo-GCP: skip de **ambas** fases y del tag; documentar en el DR.

## Cuándo

Tras `git push origin main` del repo de producto y gate git verde.

- Dev no corre si eligieron solo prod, ni si el push falló.
- Prod no corre si eligieron ambos y dev falló, si el usuario no autorizó prod, o si el tag semver no está en `origin`.

## Auth (no interactivo)

1. `VERCEL_TOKEN` en `.env` del **home** o del repo (nunca commitear). Preferir env var, **no** `--token` (se filtra en `ps`).
2. Si hay varios teams: `--scope <team>`.
3. Sin token y sin sesión (`vercel whoami` falla) → `blocked` pidiendo `vercel login` o `VERCEL_TOKEN`.

CLI: `vercel` en PATH, o `npx vercel`. Si no hay forma de correrla → `blocked`.

## Link

Correr desde el directorio del app (o el que tenga `.vercel/`).

- Debe existir `.vercel/project.json` o `.vercel/repo.json` (`vercel link` / `vercel link --repo`).
- Monorepo con app en subdir (`apps/web`): `cd` ahí, o `link --repo`.
- Sin link y sin IDs → `blocked` pidiendo `vercel link` (no adivinar org/project).

No commitear tokens. `.vercel/project.json` (orgId/projectId) sí puede ir al git si el repo ya lo versiona.

## Fase dev

`--yes` siempre en el deploy (sin prompts). **No** pases `--prod` ni `--target=production`.

```bash
cd <path-del-app-vercel>
vercel pull --yes --environment=preview
URL=$(vercel deploy --yes)
```

`URL` es el deployment que acaba de salir (host único, con hash). No es la URL estable de dev.

### Alias `-dev`

Después de ese deploy, y solo si terminó 0, apunta un alias a **esa** `URL`. El alias es el host de producción del mismo proyecto con `-dev` al final de la primera etiqueta. No es un prefijo al inicio (`dev-acme`) ni un sufijo del TLD (`acme.vercel.app-dev`).

| Host de producción | Alias |
|--------------------|--------|
| `acme.vercel.app` | `acme-dev.vercel.app` |
| `tienda.cliente.com` | `tienda-dev.cliente.com` |

Si la primera etiqueta ya termina en `-dev`, no la dupliques.

El host de producción es el dominio estable que el proyecto ya tiene en production (el de `vercel project inspect` o el alias de production que no es un deployment con hash). Si hay varios, usa el que diga el landscape o el DS; si no dicen nada, el dominio custom; si no hay custom, el `*.vercel.app` corto del proyecto. No uses el host único del deployment que acabas de crear. Si no hay host de producción, `blocked`: no inventes el alias.

```bash
vercel alias set "$URL" "$ALIAS"
```

Sin `--token`. Sin `--prod`. El CLI ignora el esquema; pasa el host.

La URL de **dev** que va al DR es `https://$ALIAS`. Anota también la `URL` cruda del deployment. No presentes ninguna de las dos como producción.

Si el deploy falla, no corras el alias. Outbox `blocked` parcial: `main` landed; no tag; no prod.  
Si el deploy ok y el alias falla (dominio ajeno, nombre tomado, dominio custom que no está en el equipo), igual `blocked` parcial: hay deployment, no hay URL estable de dev; no tag; no prod. No compres dominios ni elijas otro nombre.

## Fase prod

Solo con `approve:prod` en sí y `gate:tag` verde (tag `vX.Y.Z` en `origin` sobre el SHA de `main`).

```bash
cd <path-del-app-vercel>
vercel pull --yes --environment=production
URL=$(vercel --prod --yes)
```

`URL` sale por stdout. Anotarla en el DR como producción.

Si el build remoto falla, no fingir Live. Outbox `blocked` parcial: `main` + URL dev + tag; prod no.

## Gates

Dev:

- [ ] `vercel deploy --yes` terminó 0 y **no** llevó `--prod`
- [ ] `vercel alias set` apuntó ese deployment al host de producción con `-dev` al final de la primera etiqueta
- [ ] La URL de dev del DR es ese alias, no el host con hash
- [ ] No se usó `--token` en el comando
- [ ] No se publicó el home InfoSails

Prod:

- [ ] Hubo un sí explícito en `approve:prod` después de la URL de dev
- [ ] El tag semver ya estaba en `origin` antes de este comando
- [ ] `vercel --prod --yes` terminó 0
- [ ] Hay URL de producción
- [ ] No se usó `--token` en la línea de comando
- [ ] No se publicó el home InfoSails

Opcional: `vercel inspect <url>` para confirmar el target (`preview` en dev, `production` en prod).

## Prohibido

- Tratar la URL de dev o el alias `-dev` como producción
- Dejar el deployment de dev sin el alias `-dev`
- Correr `vercel --prod` antes del tag semver, sin el sí de `approve:prod`, o saltarse la fase dev
- `gcloud run deploy` salvo que el landscape del **mismo** artefacto sea GCP y el usuario lo pida
- Inventar una URL
- Saltar el push a `main` y publicar igual (el git va primero)
