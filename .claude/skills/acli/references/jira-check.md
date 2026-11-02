# Auditoría del proyecto Jira contra el checklist del repo

Procedimiento para verificar que el proyecto Jira tiene los custom fields y
statuses que este repo necesita, listados en `.claude/jira-required.yaml`. Dos mecanismos
distintos — no son intercambiables, cada uno cubre lo que le corresponde.

Salida esperada: **reporte en el chat de la sesión**, no un archivo persistido. No hay script que
lo genere — es una investigación guiada que el asistente repite cada vez que se invoca.

## 1. Custom fields — vía ticket modelo

Por cada work type de `.claude/jira-required.yaml` que tenga `required_fields`, se necesita un
ticket real en el proyecto con esos campos llenos de un valor identificable (el "ticket modelo").
Con ese ticket:

```bash
acli jira workitem view CLAVE-123 --fields "*all" --json \
  | jq '.fields | to_entries | map(select(.key | startswith("customfield_")) | select(.value != null and .value != []))'
```

Esto devuelve todos los `customfield_NNNNN` con valor en ese ticket. Comparar la cantidad y el
contenido contra `required_fields` del work type correspondiente en `jira-required.yaml`:

- ¿Hay tantos `customfield_` con valor como campos custom requiere ese work type?
- ¿El valor de cada uno coincide con lo que se puso en el ticket modelo para ese campo? (así se
  sabe "cuál ID es cuál campo", sin catálogo).

Repetir para cada work type con `required_fields` no vacío (Bug, Story, Task en el checklist
actual — Epic y Subtask no tienen custom fields).

**Caso especial `story_points`:** viene preconfigurado con las plantillas Scrum/Kanban de Jira
Software. No hace falta crearlo — solo confirmar que aparece en la salida del `jq` de arriba
cuando el ticket modelo tiene puntos asignados.

**Guardar los IDs.** Por cada campo de Story encontrado, copiá su `customfield_NNNNN` en la clave
correspondiente de `.claude/jira-fields.json` (`acceptance_criteria`,
`business_rules_specification`, `scope`, `out_of_scope`). Verificá:

    jq -r 'to_entries[] | "\(.key): \(.value.id)"' .claude/jira-fields.json

Ningún `id` puede quedar vacío.

## 2. Statuses — vía REST

`acli` no tiene ningún subcomando de workflows/statuses (ver `references/gotchas.md` #6). El único
camino de lectura es REST directo:

```bash
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://TU_INSTANCIA.atlassian.net/rest/api/3/project/{CLAVE}/statuses" \
  | jq '.[] | {issueType: .name, statuses: [.statuses[].name]}'
```

Esto devuelve, por cada work type del proyecto, la lista de nombres de status disponibles.
Comparar contra `required_statuses` del work type correspondiente en `jira-required.yaml` (los
nombres están en MAYÚSCULA en el checklist — comparar sin sensibilidad a mayúsculas/minúsculas si
hace falta).

**Alcance:** solo existencia de nombres, no el grafo completo de transiciones entre ellos. Si un
status existe pero no es alcanzable desde donde está el ticket, eso se descubre en vivo cuando se
use `acli jira workitem transition` — no se audita acá.

## 3. Reportar

El reporte que el asistente arma en el chat, por work type:

- Custom fields: cuáles de `required_fields` están presentes (con su ID descubierto) y cuáles
  faltan.
- Statuses: cuáles de `required_statuses` existen en el proyecto y cuáles faltan.
- Si falta algo: indicar que se crea a mano en la UI de Jira (nunca vía `acli` — no tiene esa
  superficie). Los campos de tipo `paragraph` se crean como **Paragraph** (multi-línea) y se
  publican en ADF (ver `SKILL.md`, sección Publicar texto enriquecido).
