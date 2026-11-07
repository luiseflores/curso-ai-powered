# Trampas al publicar en Jira

> **Propósito**: cuando un workflow escribe en un campo de texto enriquecido de Jira
> (descripción, AC, Alcance, Fuera de alcance, Reglas de negocio), el contenido pasa por la
> conversión Markdown → ADF (Atlassian Document Format). Dos bugs del conversor y de la API
> costaron ciclos reales. Esta guía los adelanta para no volver a chocar con ellos.
> **Cuándo usarla**: un workflow está por publicar en un campo de texto enriquecido, o aparece un
> HTTP 400 al publicar.
> **Referencias que la acompañan**: `epic-creation.md`, `add-feature.md`, `jira-operations.md`,
> `description-custom-field-dedup.md`.
> **Límite de alcance**: esta guía cubre **lo que SE ROMPE** al publicar (errores HTTP 400).

---

## Contexto

Los campos de texto enriquecido de Jira exigen ADF (un documento JSON estructurado, no Markdown).
El conversor `.claude/skills/acli/scripts/md-to-adf.ts` pasa Markdown → ADF, pero la conversión
tiene bordes conocidos y la API tiene rechazos sorpresa. Los dos casos de abajo se reproducen y
son silenciosos hasta que fallan al publicar.

---

## Gotcha 1: `md-to-adf.ts` combina marcas `code` + `strong`

- **Síntoma**: HTTP 400 `INVALID_INPUT` desde el endpoint REST de Jira (o desde `acli`) al
  publicar un campo de texto enriquecido. El error no señala ningún nodo: solo "documento
  inválido".
- **Causa**: el conversor emitía un nodo de texto con marcas `code` y `strong` a la vez cuando el
  Markdown anida `` `código` `` dentro de `**negrita**` (o cursiva). Jira rechaza esa combinación
  en la validación del documento, sin diagnóstico por nodo.
- **Arreglo permanente**: el conversor quita las marcas `strong` / `em` cuando aparece `code` en
  el mismo nodo de texto. Jira acepta los documentos que antes daban HTTP 400.
- **Hábito defensivo (igual recomendado)**: escribí el código en línea FUERA de la negrita o
  cursiva cuando el Markdown va a Jira. La transformación lo hace seguro, pero escribirlo plano
  se ve mejor en Jira y no depende de la transformación si vuelve a aparecer el bug.
  - Preferido: `**corré** \`bun install\` **primero**`
  - Tolerado (la transformación lo resuelve): `**corré \`bun install\` primero**`
  - La misma regla vale para cursiva, tachado y cualquier marca futura que se combine con `code`.

---

## Gotcha 2: el MCP rechaza varios custom fields ADF en una sola llamada

- **Síntoma**: el error `Operation value must be an Atlassian Document (see the Atlassian
  Document Format)` al actualizar varios campos de texto enriquecido en una sola llamada por MCP
  (por ejemplo `acceptance_criteria` + `scope` + `out_of_scope` juntos).
- **Causa**: el MCP convierte Markdown → ADF solo para el campo `description`. Los custom fields
  que van dentro de `additional_fields` se reenvían tal cual, así que Jira recibe Markdown donde
  espera ADF y rechaza toda la actualización.
- **Solución A: dividir la actualización**: una llamada por custom field ADF. Más lento pero
  simple y robusto. Es la opción por defecto cuando se tocan 2 o más campos.
- **Solución B: pre-convertir cada valor**: pasá cada valor Markdown por
  `.claude/skills/acli/scripts/md-to-adf.ts` y mandá el ADF resultante dentro de
  `additional_fields`.
- **Buena práctica**: con varios campos, por defecto uno por llamada (Solución A). No mezcles. La
  demora es despreciable frente a una publicación fallida que deja el issue a medio actualizar.

---

## Checklist de diagnóstico

Triage rápido cuando falla una publicación:

- HTTP 400 sin señalar ningún nodo → sospechá del Gotcha 1. Buscá `` ` `` dentro de `**…**` o
  `_…_` en el Markdown. Arreglalo en el Markdown.
- El error dice `Operation value must be an Atlassian Document` → Gotcha 2. Pasá a una llamada
  por campo, o pre-convertí cada valor.
- Cualquier otro 400 → no está cubierto acá. Mostrale el error textual al usuario; no reintentes
  en silencio.

---

## Anti-patrones

- NUNCA anides `` `código` `` dentro de `**negrita**` o `_cursiva_` en Markdown que va a un campo
  ADF.
- NUNCA mandes varios custom fields ADF en una sola llamada al MCP. No existe el caso "el primero
  anda y el resto falla": se rechaza toda la llamada, y puede quedar estado parcial.
- NUNCA te tragues en silencio un HTTP 400 o un "Operation value must be an Atlassian Document".
  Mostrale el diagnóstico al usuario con el arreglo específico (Gotcha 1 o 2). Un reintento sin
  el arreglo falla igual.
- NUNCA apliques la Solución B con una copia vieja del conversor: usá siempre la de
  `.claude/skills/acli/scripts/`.
