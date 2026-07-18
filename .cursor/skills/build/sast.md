# Agente `sast` — Análisis estático de seguridad (Build)

Gate de **SAST** (Static Application Security Testing) del proceso Build.  
Complementa TDD y mutación: no los sustituye.

## Dónde encaja en el ciclo

```text
tdd-dev (Red incluye pruebas de seguridad cuando aplica)
    → coverage-gate
    → [ sast ∥ mutation ∥ e2e ]   ← subagentes en paralelo
    → integrator
```

| Capa | Qué cubre | Qué no cubre |
|------|-----------|--------------|
| **TDD** (`tdd-dev`) | Comportamiento de seguridad **específico de la historia** (authz, validación, “no filtrar PII”) como tests Red→Green | Patrones OWASP genéricos en todo el árbol |
| **Mutación** | Que los tests (incl. seguridad) maten mutantes | Secrets hardcodeados, deps vulnerables, misconfig |
| **SAST** (`sast`) | Hallazgos estáticos en el **código de la historia** (inyección, secretos, crypto débil, misconfig) | Comportamiento dinámico / flujos e2e |

SAST corre **después** de cobertura (código de la historia ya escrito) y **en paralelo** con mutación y e2e: no depende de ellas.

## Herramientas (elegir según stack)

Preferir una ya en el repo; si no hay, instalar/configurar una razonable:

| Stack | Opciones |
|-------|----------|
| JS/TS | **Semgrep**, ESLint security / `@typescript-eslint`, `npm audit` (deps) |
| Python | Semgrep, Bandit, `pip-audit` |
| Go | Semgrep, gosec |
| Java/Kotlin | Semgrep, SpotBugs + FindSecBugs |
| Genérico | **Semgrep** (default org si no hay otra) |

Documentar herramienta + comando + versión en el Build Report.  
No fingir PASS sin correr el análisis.

## Alcance

- Paths / paquetes tocados por la historia (diff o scope del DS), no necesariamente todo el monorepo (salvo política del repo).
- Severidades que fallan el gate por defecto: **ERROR / HIGH / CRITICAL** (o equivalente Semgrep `ERROR`).
- WARNING/INFO: listar en el report; no bloquean salvo que el DS/guardrails digan lo contrario.
- Findings **preexistentes** fuera del scope: no exigen fix en esta historia; documentar “fuera de scope”.
- Findings **introducidos o en paths de la historia**: fix o `blocked` / no `complete`.

## Durante `tdd-dev` (antes del agente sast)

En Red, cuando el BP/DS implique riesgo:

- Tests de autorización (rol A no accede a recurso de B).
- Validación de input / rechazo de payloads peligrosos si es parte del Core.
- “No loguear secretos / tokens” si es requisito.

Opcional (rápido): Semgrep solo sobre archivos del WP antes de marcar el WP `done` — feedback temprano; el gate formal sigue siendo el agente `sast`.

## Ejecución del agente

1. Confirmar herramienta instalada; si falta → instalar o **pedir** al usuario / `blocked`.
2. Correr SAST sobre el scope de la historia.
3. Clasificar hallazgos: in-scope vs preexistente; severidad.
4. Si hay HIGH/CRITICAL/ERROR in-scope → fallar gate (Lead decide fix en tdd-dev o blocked).
5. Reportar tabla al Lead / Build Report.
6. Puede lanzarse como **subagente** en paralelo con `mutation` y `e2e`.

## Prerrequisitos

- Acceso a correr el CLI (Semgrep, etc.).
- Si el repo usa Semgrep App / CodeQL en CI: preferir la misma config local (`.semgrep.yml`, codeql packs).
- `npm audit` / SCA de deps: incluir si el WP tocó `package.json` / lockfile; fallos HIGH/CRITICAL de deps **nuevas** → gate rojo o documentar excepción aceptada.

## Checklist

- [ ] Herramienta identificada y comando documentado
- [ ] Scope = código de la historia
- [ ] Cero hallazgos bloqueantes in-scope (o excepción ADR/usuario documentada)
- [ ] Tabla de hallazgos (aunque esté vacía) en el Build Report
- [ ] No PASS inventado
