---
name: acli
description: "Atlassian CLI (binario oficial `acli`) para operar Jira Cloud desde la terminal: crear, ver, editar y transicionar tickets, y auditar la configuración del proyecto (custom fields, statuses) contra el checklist del repo. Se usa cada vez que el asistente necesita tocar Jira sin pasar por la UI. Disparadores: `acli`, Jira desde la terminal, crear/ver/transicionar un ticket, auditar Jira, custom field de Jira, ID de campo custom."
license: MIT
compatibility: [claude-code, copilot, cursor, codex, opencode]
allowed-tools: Bash(acli:*), Bash(curl:*), Bash(jq:*)
complementary_categories: [issue-tracker]
---

# Atlassian CLI (`acli`)

`acli` es el CLI oficial de Atlassian para Jira Cloud. Este repo lo usa como camino principal
para operar el proyecto Jira — la skill `atlassian` MCP (si está configurada) es
solo respaldo, nunca el camino por defecto.

Alcance de esta skill: **un solo proyecto Jira**, con los IDs de custom fields cargados a mano en
`.claude/jira-fields.json` y sin administración de instancia. Los campos largos (Criterios de
aceptación, Reglas de negocio, Alcance, Fuera de alcance) son de tipo Paragraph, así que se
publican en ADF con el conversor `scripts/md-to-adf.ts`.

## Por qué esta skill existe

`acli` tiene comportamientos que sorprenden si no se conocen de antemano:

1. **No enumera custom fields.** `acli jira field` solo crea/edita/borra definiciones — no hay
   `list`. Para descubrir el ID de un campo hay que inspeccionar un ticket que ya lo tenga seteado.
2. **`workitem edit` rechaza custom fields.** Solo `workitem create` los acepta. Editar un custom
   field en un ticket ya existente necesita REST `PUT`, no `acli`.
3. **No hay administración de workflows/statuses.** Ni siquiera existe `acli jira workflow --help`
   — cae silenciosamente al help del comando padre (exit 0, sin error). "Sin error" no significa
   "el comando existe".
4. **Los subcomandos inventados no dan error.** Si escribís algo que no existe, `acli` muestra el
   help del nivel anterior y sale con éxito. Siempre verificá que el texto del help cambió.

## Comando estructura

```
acli jira <recurso> <acción> [flags]
```

Todo tiene `--help`. Usalo seguido: `acli jira workitem create --help`.

## Quick start

```bash
# 1. Autenticar con API Token (ver references/auth.md)
echo "$ATLASSIAN_API_TOKEN" | acli jira auth login \
  --site "TU_INSTANCIA.atlassian.net" \
  --email "$ATLASSIAN_EMAIL" \
  --token

# 2. Verificar
acli jira auth status

# 3. Ver un ticket con TODOS los campos (necesario para ver customfield_*)
acli jira workitem view CLAVE-123 --fields "*all" --json | jq '.fields'
```

## Navegación — cuándo cargar cada referencia

| Si el asistente necesita… | Cargar |
|---|---|
| Loguearse, verificar sesión, resolver error de auth | `references/auth.md` |
| Crear, ver, editar, transicionar o vincular un ticket | `references/workitem.md` |
| Publicar descripción o campos largos (ADF) | sección "Publicar texto enriquecido (ADF)" de este archivo |
| Entender un comportamiento sorprendente o un error raro | `references/gotchas.md` |
| Auditar el proyecto Jira real contra el checklist del repo | `references/jira-check.md` |

No precargues todas — solo la que corresponde a la tarea actual.

## Reglas que siempre aplican

- **Nunca hardcodear `customfield_NNNNN`.** Cada instancia Jira tiene sus propios IDs. Se
  descubren con el ticket modelo (ver `references/workitem.md` sección Custom fields), se guardan
  en `.claude/jira-fields.json` y se leen de ahí en el momento (`jq -r '.<clave>.id'`).
- **Nunca asumir que `acli` acepta custom fields en `workitem edit`.** Rechaza con error duro,
  exit 1. El único camino para editar un custom field existente es REST `PUT` con `curl` — ver
  `references/gotchas.md`.
- **Nunca intentar administrar workflows/statuses vía `acli`.** No existe esa superficie. Auditar
  statuses es de solo lectura, vía REST — ver `references/jira-check.md`.
- **`--status` en `transition` es el NOMBRE del status, no un ID.** Debe ser alcanzable desde el
  status actual según el workflow del proyecto.

## Publicar texto enriquecido (ADF)

Jira guarda la descripción, los comentarios y todo campo Paragraph como **ADF** (Atlassian
Document Format), un árbol JSON. `acli` **nunca** convierte Markdown: si le pasás `## Título`,
guarda ese texto literal. Flujo por defecto:

1. Escribir el contenido en Markdown.
2. Convertirlo: `bun .claude/skills/acli/scripts/md-to-adf.ts entrada.md salida.adf.json`
   (valida antes de escribir; si falla, no genera el archivo).
3. Meter el ADF en el payload de `create --from-json`, dentro de `additionalAttributes`:

```bash
ID_AC=$(jq -r '.acceptance_criteria.id' .claude/jira-fields.json)
jq -n --arg pk "CLAVE" --arg ac "$ID_AC" \
  --slurpfile desc /tmp/desc.adf.json --slurpfile acd /tmp/ac.adf.json \
  '{projectKey: $pk, type: "Story", summary: "Carrito | Aplicar cupón de descuento",
    description: $desc[0], parentIssueId: "CLAVE-10",
    additionalAttributes: {($ac): $acd[0]}}' > /tmp/story.json
acli jira workitem create --from-json /tmp/story.json --json
```

4. Leer de vuelta: `acli jira workitem view CLAVE-11 --fields "*all" --json`. Jira puede
   descartar en silencio nodos inválidos; solo la lectura de vuelta lo detecta.

**Padre en `--from-json`:** el campo es `parentIssueId` y acepta una clave (`CLAVE-10`).
`parentIssueKey` no existe y da `unknown field`.

**Editar un campo Paragraph ya creado:** `acli workitem edit` rechaza custom fields. Se usa REST
`PUT` con el ADF dentro de `fields` (no `additionalAttributes`):

```bash
jq -n --arg id "$ID_AC" --slurpfile acd /tmp/ac.adf.json '{fields: {($id): $acd[0]}}' > /tmp/put.json
bun .claude/skills/acli/scripts/md-to-adf.ts --check <(jq ".fields[\"$ID_AC\"]" /tmp/put.json)
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" -X PUT \
  -H "Content-Type: application/json" --data @/tmp/put.json \
  "$ATLASSIAN_URL/rest/api/3/issue/CLAVE-11"
```

Las variables `ATLASSIAN_*` llegan porque Claude se abre con `bun run claude`. Nunca se imprimen
ni se pegan en el chat.

**Gotcha de marcas:** no anidar `` `código` `` dentro de `**negrita**`: Jira responde HTTP 400.

## Checklist del proyecto

El checklist completo de custom fields y statuses requeridos vive en `.claude/jira-required.yaml`
(5 work types: Épica, Bug, Story, Task, Subtask). `references/jira-check.md` documenta cómo
auditar el proyecto Jira real contra ese archivo.
