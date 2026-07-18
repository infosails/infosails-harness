# Estándares de Build (InfoSails)

Obligatorios para el **Build Lead** y sus agentes. No negociables sin ADR Accepted.

## TDD

Orden por unidad de trabajo:

1. **Red** — escribir prueba que falla (comportamiento del Design Package / Gherkin).
   Incluir **pruebas de seguridad de comportamiento** cuando el Core lo exija (authz, validación, no filtrar secretos/PII).
2. **Green** — mínima implementación que pase.
3. **Refactor** — limpiar sin bajar cobertura ni romper pruebas.

Prohibido: implementar producción sin prueba roja previa en esa unidad.

SAST (análisis estático) **no** sustituye el Red de seguridad: el estático lo corre el agente `sast` tras cobertura; ver [sast.md](sast.md).

## Cobertura

| Métrica | Mínimo |
|---------|--------|
| Line / statement coverage (código tocado por la historia) | **≥ 85%** |
| Preferible también branch coverage en código nuevo | ≥ 85% si la herramienta lo reporta |

Si la suite del repo mide global: el diff/nuevo código de la historia debe cumplir ≥ 85%.  
Gate rojo → no `complete`.

## Mutación

- Ejecutar pruebas de **mutación** sobre el código de la historia (o paquete afectado).
- Umbral por defecto: **mutation score ≥ 70%** en el scope de la historia (ajustar solo con ADR).
- Si no hay runner de mutación en el stack: instalar/configurar uno razonable al stack (Stryker, mutmut, PITest, etc.) o `blocked` explicando el gap — no fingir mutación.

## Mutación

- Ejecutar pruebas de **mutación** sobre el código de la historia (o paquete afectado).
- Umbral por defecto: **mutation score ≥ 70%** en el scope de la historia (ajustar solo con ADR).
- Si no hay runner de mutación en el stack: instalar/configurar uno razonable al stack (Stryker, mutmut, PITest, etc.) o `blocked` explicando el gap — no fingir mutación.
- Las pruebas de seguridad del TDD también deben contribuir a matar mutantes (no dejar authz sin assert efectivo).

## SAST — análisis estático de seguridad

Tras **coverage-gate**, en **paralelo** con mutación y e2e:

```text
coverage-gate → [ sast ∥ mutation ∥ e2e ]
```

- Agente: `sast` — guía [sast.md](sast.md).
- Default razonable: **Semgrep** (u herramienta ya del repo).
- Gate rojo si hay hallazgos **ERROR/HIGH/CRITICAL** en el scope de la historia.
- No sustituye TDD ni mutación; cubre secretos, inyección, misconfig, deps vulnerables.
- Si no se puede correr la herramienta → pedir instalación o `blocked` — no fingir PASS.

## Paralelismo

- El `planner` parte el Design Package en **work packages** independientes.
- Maximizar paquetes sin dependencia; ejecutar en paralelo cuando el entorno lo permita (varios agentes / jobs).
- Dependencias explícitas en el plan; no paralelizar lo acoplado.
- Tests unitarios/integración en paralelo si el framework lo soporta.

## E2E — Playwright + Gherkin (obligatorio cuando hay UI/flujo)

### Herramienta
- E2E con **Playwright** (no Cypress u otro, salvo ADR Accepted de excepción).
- Si Playwright no está en el repo: **instalarlo y configurarlo** como parte del Build (deps, `playwright.config`, scripts npm/pnpm).
- Garantizar que se pueda ejecutar: `npx playwright install` (browsers) si falta.

### Fuente de verdad: escenarios Gherkin del blueprint
- Cada escenario **Dado que / Cuando / Entonces** del BP debe mapearse a una prueba Playwright (o step compartido).
- Nombrar tests/archivos de forma trazable (ej. `e2e/bp-…-escenario-1.spec.ts`).
- En el Build Report: tabla **Gherkin → Playwright** (PASS/FAIL por escenario).
- Fallo de un escenario Gherkin vía Playwright → gate rojo → no `complete`.

### Cuándo skip e2e
Solo si el cambio es **puro interno** sin borde usuario/HTTP observable (lib de domain sin adapter).  
Documentar `e2e_skipped` + razón. Si el BP tiene escenarios de UI/API y se skipea sin justificación → inválido.

### Garantizar que se pueda probar
Antes de correr e2e, el Build debe tener (o **pedir**):

| Necesidad | Ejemplo |
|-----------|---------|
| App alcanzable | `BASE_URL` / preview URL / `localhost` levantado |
| Playwright + browsers | instalados y script `test:e2e` |
| Datos / usuarios de prueba | credenciales de test (pedir si faltan) |
| Secretos de entorno de test | mismos criterios que § Prerrequisitos |

Si no se puede ejecutar: **pedir al usuario** lo faltante o `blocked` — no marcar e2e PASS inventado.

## Prerrequisitos (claves, secretos, acceso, **capacidad de probar**)

Antes de implementar o de correr gates que dependan de servicios reales **o de e2e**:

1. **Inventariar** lo necesario (del DS, landscape, `.env.example`, docs, código, **escenarios Gherkin**):
   - API keys, tokens, client secrets
   - Connection strings / credenciales DB
   - Project IDs (Vercel, GCP), service accounts
   - **BASE_URL** / entorno donde Playwright pegará
   - Usuarios/datos de prueba para los Gherkin
   - Playwright instalado + browsers
2. **Comprobar** si ya existen de forma segura (env, `.env` local no commiteado — **sin imprimir valores**).
3. Si falta algo **imprescindible** (incl. no poder correr Playwright):
   - **Pedirlo al usuario** con lista explícita.
   - Outbox `blocked` si bloquea el Build.
   - **No** inventar claves ni fingir e2e en verde.
4. Tras recibirlos: confirmar (sin eco del valor) y continuar.

Plantilla de pedido:

```text
Para poder construir y probar (Playwright + Gherkin) necesito:
1. BASE_URL — URL de la app (local o preview)
2. NOMBRE_VAR — para qué
3. Usuario/clave de prueba (si el escenario lo requiere)
4. Confirmación para instalar Playwright browsers si faltan
…
```

## Gates de salida (todos verdes)

- [ ] Prerrequisitos (claves/secretos/acceso) resueltos o skip justificado
- [ ] Capacidad de probar e2e garantizada (Playwright listo + URL/datos) o skip Gherkin justificado
- [ ] TDD seguido por work package (incl. tests de seguridad de comportamiento si aplica)
- [ ] Cobertura ≥ 85% (scope historia)
- [ ] Mutación ejecutada y score ≥ umbral
- [ ] SAST ejecutado sin hallazgos bloqueantes in-scope (o excepción documentada)
- [ ] E2E Playwright ejecutado mapeando Gherkin, o skip justificado
- [ ] Cada escenario Gherkin del BP: PASS en Playwright (o N/A justificado)
- [ ] Guardrails del Design Package respetados

Cualquier gate rojo → no outbox `complete`.
