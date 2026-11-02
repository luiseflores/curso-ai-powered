# Tickets (`acli jira workitem`)

> Nota de terminología: Atlassian renombró `issue` → `workitem` en la UI y el CLI, pero el JSON
> de respuesta sigue usando `issues[]` como array de nivel superior, y los custom fields siguen
> llamándose `customfield_NNNNN`. El rename es solo de superficie — no cambia el JSON.

## El patrón de selector

Los comandos que operan sobre un ticket existente (`view`, `edit`, `transition`) aceptan
`-k, --key CLAVE-123` para apuntar a uno específico. Este repo trabaja siempre ticket por
ticket — no hace falta el resto de selectores masivos (`--jql`, `--filter`) que sí usa el
ecosistema completo.

## `create` — crear un ticket

```bash
# Flags directos — alcanza para la mayoría de los casos
acli jira workitem create \
  --project "CLAVE" \
  --type "Story" \
  --summary "Agregar validación de email en el registro" \
  --assignee "@me"

# Con custom fields — necesita el payload JSON completo
acli jira workitem create --generate-json > ticket.json   # scaffold
# editar ticket.json a mano, agregando additionalAttributes
acli jira workitem create --from-json ticket.json --json
```

Custom fields en `create` van en un bloque `additionalAttributes`, por ID numérico (nunca por
nombre):

```json
{
  "summary": "Agregar validación de email en el registro",
  "type": "Story",
  "projectKey": "CLAVE",
  "additionalAttributes": {
    "customfield_10087": "Texto del campo Reglas de negocio",
    "customfield_10090": 5
  }
}
```

Formas de valor documentadas: texto plano (string) para campos de una línea, número (bare), o
single-select (`{"value": "..."}`). Los campos Paragraph (multi-línea) llevan un documento ADF
completo: ver la sección "Publicar texto enriquecido (ADF)" de `SKILL.md`.

## `view` — ver un ticket

```bash
# Vista default — NO trae customfield_*
acli jira workitem view CLAVE-123

# Con TODOS los campos — imprescindible para auditoría/discovery de IDs
acli jira workitem view CLAVE-123 --fields "*all" --json | jq '.fields'
```

Vista default: `key, issuetype, summary, status, assignee, description`. Sin `--fields "*all"`
nunca vas a ver ningún `customfield_` — es el error más común.

## `edit` — editar un ticket existente

```bash
acli jira workitem edit --key "CLAVE-123" --summary "Título corregido" --yes
```

Flags soportados: `--summary`, `--description`, `--description-file`, `--assignee`, `--labels`,
`--type`. **Nada más.**

**Gotcha crítico: `edit` rechaza cualquier custom field, con error duro (exit 1).** No importa la
forma del payload (`additionalAttributes`, `fields`, o el campo suelto) — todas dan
`✗ Error: json: unknown field ...`. El único camino para editar un custom field en un ticket ya
creado es REST `PUT` — ver `references/gotchas.md`.

## `transition` — cambiar de status

```bash
acli jira workitem transition --key "CLAVE-123" --status "En desarrollo"
```

`--status` es el **nombre** del status, no un ID. Tiene que ser alcanzable desde el status actual
según el workflow real del proyecto — si no lo es, `acli` falla (y eso es esperable, no un bug).

## Custom fields — cómo encontrar el ID

`acli` no puede listar custom fields (`acli jira field` solo crea/edita/borra definiciones, no
enumera). Tres formas de conseguir el ID sin escribir código:

```bash
# 1. Desde un ticket que ya tenga el campo lleno (ticket modelo)
acli jira workitem view CLAVE-123 --fields "*all" --json \
  | jq '.fields | to_entries | map(select(.key | startswith("customfield_")) | select(.value != null and .value != []))'

# 2. Desde la URL del admin de Jira al editar el campo
# https://TU_INSTANCIA.atlassian.net/secure/admin/EditCustomField!default.jspa?id=NNNNN

# 3. Desde REST — lista TODOS los campos del site
curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  "https://TU_INSTANCIA.atlassian.net/rest/api/3/field" | jq '.[] | {id, name, custom}'
```

## `link create` — vincular tickets (con inversión de flags)

Los nombres `--out` y `--in` sugieren "outward" e "inward" de Jira, pero **`acli` los invierte**:

- `--out` recibe el **prerrequisito / el trabajo bloqueado** (el lado inward en la UI).
- `--in` recibe el **dependiente / el que bloquea** (el lado outward en la UI).

| Tipo | Outward (UI) | Inward (UI) | `--out` recibe | `--in` recibe |
|---|---|---|---|---|
| `Blocks` | blocks | is blocked by | el trabajo bloqueado | el que bloquea |
| `Relates` | relates to | relates to | cualquiera | cualquiera (simétrico) |

```bash
# Intención: "CLAVE-12 BLOQUEA a CLAVE-13" (12 bloquea, 13 queda bloqueada)
acli jira workitem link create --out CLAVE-13 --in CLAVE-12 --type "Blocks" --yes

# Verificación obligatoria
acli jira workitem link list --key CLAVE-12 --json
# Esperado: outwardIssueKey == CLAVE-13
# Si quedó al revés: acli jira workitem link delete --id <linkId> --yes y recrear con los flags cruzados
```

`Relates` es simétrico: no hay dirección que verificar.
