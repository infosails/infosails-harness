# Ejemplo de Feature Blueprint

Ejemplo de referencia para el Discovery Agent. No es un blueprint activo del índice.

## Conversación resumida

Usuario: "Necesito que el backoffice muestre el estado de sync de cada póliza."

Agente clarifica: módulo Backoffice Riesgos, solo lectura, sin re-sync manual en esta iteración.

## Markdown resultante

```markdown
# BP-20260717-001 Feature Blueprint: Estado de sync de pólizas

> **Epic/Contexto:** Backoffice Riesgos — Visibilidad operativa
> **Status:** Ready for Build
> **Creado por:** Discovery Agent

## 1. El Problema (Contexto de Usuario)
<!-- Breve descripción de qué problema real tiene el usuario y por qué se necesita esto. Máximo 3 líneas. Ayuda al LLM de desarrollo a entender el propósito del código. -->
* Operaciones no sabe si una póliza está desfasada respecto al sistema fuente.
* Hoy revisan logs manualmente y pierden tiempo en escalamientos.
* Necesitan ver el estado de sync en el detalle de póliza.

## 2. El Core (Alcance Técnico Requerido)
<!-- Lista de las funcionalidades mínimas que DEBEN ser implementadas. Enfocado en el "QUÉ". -->
* [ ] Mostrar badge de estado de sync (ok / stale / error) en el detalle de póliza
* [ ] Mostrar timestamp de última sync exitosa
* [ ] Si hay error, mostrar mensaje corto ya persistido en el backend

## 3. Límites y Exclusiones (Out of Scope / Guardrails)
<!-- ESTA SECCIÓN ES CRUCIAL PARA AGENTES. Enumera explícitamente qué NO debe hacer el agente de desarrollo para evitar consumo innecesario de tokens, refactorizaciones infinitas o alucinaciones. -->
* **NO** implementar botón ni flujo de re-sync manual
* **NO** cambiar el job/pipeline de sincronización
* **NO** refactorizar el listado de pólizas ni el modelo de datos

## 4. Criterios de Aceptación (Casos de Prueba Gherkin)
<!-- Escenarios atómicos que el Agente de QA o pruebas unitarias usará para validar que funciona. -->

### Escenario 1: Póliza con sync OK
* **Dado que** una póliza tiene sync_status=ok y last_synced_at conocido
* **Cuando** el usuario abre el detalle de esa póliza
* **Entonces** ve el badge "ok" y el timestamp de última sync

### Escenario 2: Póliza con error de sync
* **Dado que** una póliza tiene sync_status=error y un mensaje de error
* **Cuando** el usuario abre el detalle de esa póliza
* **Entonces** ve el badge "error" y el mensaje corto de error
```

## JSON companion (recortado)

```json
{
  "id": "BP-20260717-001",
  "title": "Estado de sync de pólizas",
  "epic": "Backoffice Riesgos — Visibilidad operativa",
  "status": "Ready for Build",
  "created_by": "Discovery Agent",
  "problem": [
    "Operaciones no sabe si una póliza está desfasada respecto al sistema fuente.",
    "Hoy revisan logs manualmente y pierden tiempo en escalamientos.",
    "Necesitan ver el estado de sync en el detalle de póliza."
  ],
  "core": [
    { "text": "Mostrar badge de estado de sync (ok / stale / error) en el detalle de póliza", "done": false },
    { "text": "Mostrar timestamp de última sync exitosa", "done": false },
    { "text": "Si hay error, mostrar mensaje corto ya persistido en el backend", "done": false }
  ],
  "out_of_scope": [
    "implementar botón ni flujo de re-sync manual",
    "cambiar el job/pipeline de sincronización",
    "refactorizar el listado de pólizas ni el modelo de datos"
  ],
  "acceptance_criteria": [
    {
      "name": "Póliza con sync OK",
      "given": "una póliza tiene sync_status=ok y last_synced_at conocido",
      "when": "el usuario abre el detalle de esa póliza",
      "then": "ve el badge \"ok\" y el timestamp de última sync"
    },
    {
      "name": "Póliza con error de sync",
      "given": "una póliza tiene sync_status=error y un mensaje de error",
      "when": "el usuario abre el detalle de esa póliza",
      "then": "ve el badge \"error\" y el mensaje corto de error"
    }
  ],
  "markdown_path": "memory/blueprints/BP-20260717-001-estado-sync-polizas.md",
  "created_at": "2026-07-17T20:00:00Z",
  "phase": "discovery"
}
```
