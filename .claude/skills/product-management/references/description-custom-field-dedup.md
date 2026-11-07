# Descripción ↔ custom fields: sin duplicación

> **Propósito**: fijar el contrato de fuente única de verdad entre la descripción de un issue de
> Jira y sus custom fields de texto enriquecido. AC, Alcance y Fuera de alcance viven en su
> propio campo, NUNCA en la descripción. Todo workflow que escribe una historia respeta este
> contrato, o la duplicación vuelve.
> **Cuándo usarla**: al crear una historia, al corregir una existente o al auditar una historia.
> **Referencias que la acompañan**: `add-feature.md`, `epic-creation.md`,
> `jira-publishing-gotchas.md` (casos borde al publicar).

---

## Por qué existe este contrato

Guardar AC, Alcance o Fuera de alcance dentro de la descripción crea dos copias del mismo
contenido: una en la descripción y otra en el custom field. Las dos se desincronizan. Quien
consume la historia después (el equipo dev, el diseño de pruebas de QA) lee los custom fields:
lo que vive solo en la descripción queda invisible. Lo que vive en los dos lugares obliga a
adivinar cuál es el correcto. Un solo lugar por concepto.

---

## Secciones de la descripción: lo que SÍ va

En este orden, de arriba hacia abajo:

1. **`## Historia de usuario`**: narrativa Como / Quiero / Para. Siempre presente.
2. **`## Reglas de negocio`**: solo desborde. Se usa cuando el contenido no entra en
   `business_rules_specification` (tablas, enumeraciones largas). El custom field sigue siendo
   el canónico; esta sección lo referencia explícitamente.

Nada que no esté en esta lista va en la descripción.

**Notas técnicas** (pistas para quien implemente): no van en la descripción ni en los custom
fields. Van como **comentario** del ticket con encabezado `## Notas técnicas`.

---

## Secciones que NUNCA van en la descripción

| Sección prohibida | Lugar canónico |
|---|---|
| `## Criterios de aceptación` | `acceptance_criteria` |
| `## Alcance` | `scope` |
| `## Fuera de alcance` | `out_of_scope` |

Si ves alguno de estos encabezados en una descripción, la historia tiene un bug de duplicación.
Corré la auditoría de abajo.

---

## Contrato Alcance vs Fuera de alcance

`scope` lleva lo que la historia SÍ hace: la lista de lo que entra, el límite afirmativo.
`out_of_scope` lleva lo que la historia NO hace: exclusiones explícitas, trabajo diferido, cosas
que alguien podría suponer incluidas y no lo están. Sin mezclar los dos campos: poner ítems de
alcance y de fuera de alcance en el mismo campo es un bug, aunque estén bien rotulados. Cada
campo tiene un solo trabajo.

---

## Procedimiento de auditoría

Devuelve un reporte estructurado al usuario.

1. Leé la historia con la operación "Leer un ticket con custom fields" de `jira-operations.md`.
   Incluí la descripción Y los tres custom fields (`acceptance_criteria`, `scope`, `out_of_scope`, con sus
   IDs de `.claude/jira-fields.json`).
2. Revisá la descripción. Detectá cualquiera de los encabezados prohibidos (sin distinguir
   mayúsculas; variantes como `## AC` o `## Criterios` también cuentan).
3. Por cada sección prohibida encontrada:
   - Leé el contenido de la sección.
   - Leé el custom field canónico correspondiente.
   - Compará. Si el contenido ya está en el campo (igual o contenido en él) → "se puede quitar".
   - Si NO está (la descripción tiene más, o el campo está vacío) → **migrar primero**: escribí
     lo que falta en el custom field (operación "Actualizar un custom field ADF existente" de
     `jira-operations.md`) y recién ahí "se puede quitar".
4. Quitá cada sección prohibida de la descripción. Conservá todo lo demás textual.
5. Usá `[ISSUE_TRACKER_TOOL]` para actualizar la descripción: una sola escritura, sin mezclarla
   con los custom fields (ver `jira-publishing-gotchas.md` Gotcha 2).
6. Mostrale al usuario, en un solo reporte:
   - Qué secciones prohibidas se detectaron.
   - Para cada una: si el contenido se migró a su campo o ya estaba y solo se quitó.
   - Estado final: la descripción sigue el formato canónico.

Si no hay secciones prohibidas, respondé en una línea "la descripción está limpia" y terminá.

---

## Anti-patrones

- NUNCA copies AC, Alcance o Fuera de alcance en la descripción "para que se vea". Los custom
  fields SON la capa visible; la descripción es para la narrativa.
- NUNCA mezcles ítems de alcance y de fuera de alcance en el mismo campo.
- NUNCA quites una sección prohibida sin verificar antes que el contenido existe en el custom
  field canónico. Perder datos en silencio es el peor resultado de esta auditoría.
- NUNCA mezcles la actualización de la descripción con la de los custom fields en una sola
  llamada al MCP: el Gotcha 2 de `jira-publishing-gotchas.md` la rechaza.
- NUNCA corras la auditoría sobre una historia en desarrollo sin coordinar con el dev: quitarle
  AC de la descripción mientras la tiene abierta genera confusión. Auditá al refinar, no durante
  la ejecución.
