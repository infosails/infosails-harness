# Vercel — producción después de git

El Deploy Lead, **después** de pushear `main` en el git de producto, publica a **producción** con la CLI de Vercel.

Solo aplica a deployables en **Vercel** (landscape / DS / `.vercel`). El **home** (`memory/`) no se publica. Backends solo-GCP: skip y documentar en el DR.

## Cuándo

Tras `git push origin main` del repo de producto y gate git verde. No publicar si el push falló.

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

## Comando

`--yes` siempre (sin prompts). Producción:

```bash
cd <path-del-app-vercel>
vercel pull --yes --environment=production
URL=$(vercel --prod --yes)
```

`URL` sale por stdout. Anotarla en el DR.

Si el build remoto falla, no fingir Live. Outbox `blocked` con el error de la CLI.

## Gate Vercel

- [ ] El comando terminó 0
- [ ] Hay URL `https://…vercel.app` o dominio de prod
- [ ] No se usó `--token` en la línea de comando
- [ ] No se publicó el home InfoSails

Opcional: `vercel inspect <url>` o `vercel ls` para confirmar `target: production`.

## Prohibido

- Publicar **preview** (`vercel` sin `--prod`) como el deploy de este proceso
- `gcloud run deploy` salvo que el landscape del **mismo** artefacto sea GCP y el usuario lo pida; el contrato default de publish es Vercel
- Inventar una URL de prod
- Saltar el push a `main` y solo hacer `vercel --prod` (el git va primero)
