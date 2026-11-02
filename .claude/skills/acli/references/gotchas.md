# Gotchas — comportamientos sorprendentes de `acli`

Todo lo que la documentación oficial no deja obvio, recortado a lo que este repo realmente toca.

## 1. Paginación silenciosa

`search` y otros comandos que listan resultados se cortan en el límite del server (30-50 filas)
si no pasás `--paginate` — sin aviso, sin error. Este repo trabaja ticket por ticket, así que no
suele importar, pero si algún día contás o iterás resultados, pasá `--paginate` siempre.

## 2. "Issue" vs "workitem" es solo de superficie

Atlassian renombró `issue` → `workitem` en la UI y el CLI, pero el JSON de respuesta sigue usando
`issues[]` como array de nivel superior. Al escribir un filtro `jq`, usá `.issues[]` — no
`.workitems[]`.

## 3. Custom fields: forma del payload en `create`

`acli jira workitem create --from-json` espera los custom fields en un bloque
`additionalAttributes`, con ID numérico (`customfield_NNNNN`), nunca por nombre:

```json
{
  "summary": "...",
  "type": "Story",
  "projectKey": "CLAVE",
  "additionalAttributes": {
    "customfield_10087": "texto",
    "customfield_10090": 5
  }
}
```

## 4. Custom fields NO se pueden editar vía `acli`

`acli jira workitem edit` rechaza cualquier custom field con error duro, exit 1 — probado contra
las tres formas posibles del payload (`additionalAttributes`, `fields`, campo suelto). El único
camino para editar un custom field en un ticket ya existente es REST `PUT`:

```bash
curl -sS -w "\nHTTP %{http_code}\n" \
  -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" \
  -X PUT "https://TU_INSTANCIA.atlassian.net/rest/api/3/issue/CLAVE-123" \
  -H "Content-Type: application/json" \
  -d '{"fields": {"customfield_10087": "texto actualizado"}}'
# Esperado: HTTP 204 (sin cuerpo en la respuesta)
```

Nota: la envoltura REST es `fields`, distinta de `additionalAttributes` que usa `create` — no son
intercambiables.

## 5. Custom fields NO se pueden enumerar vía `acli`

`acli jira field` solo crea/edita/borra definiciones — no hay `list`. Para descubrir IDs, ver
`references/workitem.md` sección "Custom fields — cómo encontrar el ID" (ticket modelo + `jq`,
URL del admin, o REST `GET /rest/api/3/field`).

## 6. Cero administración de workflows/statuses

`acli` no tiene NINGÚN subcomando para workflows, workflow schemes, ni statuses — ni siquiera
existe `acli jira workflow --help` (cae al help del comando padre, exit 0, sin error). "Sin error"
no significa "el comando existe": siempre verificá que el texto del help cambió. Auditar statuses
es de solo lectura, vía REST — ver `references/jira-check.md`.

## 7. `transition` matchea por nombre de status, no por ID

`--status` acepta el nombre del status destino, no un ID de transición. Si dos transiciones
distintas llevan al mismo status con validaciones distintas, `acli` puede elegir la equivocada y
fallar. No hay escape hatch (`--transition-id`) en el CLI — si eso pasa, hay que ir a REST
directo contra `/rest/api/3/issue/{key}/transitions`.

## 8. Auth es por producto — Jira no autentica Confluence ni admin

Loguearse con `acli jira auth login` NO autentica `acli confluence` ni `acli admin` — cada
producto guarda su propia sesión. Este repo solo usa el namespace de Jira; si alguna vez ves un
error `forbidden` en otro comando `acli`, la causa más común es esta.

## 9. ADF en campos Paragraph vía `create --from-json` (verificado)

Verificado contra Jira Cloud el 2026-10-11: un documento ADF dentro de `additionalAttributes`
para un campo Paragraph se guarda tal cual. Un bloque ```` ```gherkin ```` convertido con
`md-to-adf.ts` se lee de vuelta como `codeBlock` con `language: gherkin` y en la UI se ve como
bloque de código. `parentIssueId` con la clave de una épica la cuelga como hija.

Otro dato útil: la respuesta de `acli jira workitem view --fields "*all" --json` no trae `names`
ni `schema`, así que un custom field vacío no se puede identificar por nombre. Por eso el ticket
modelo tiene que tener cada campo lleno.
