# Guía para el equipo Dev — Generación y Mantenimiento del Contexto de Proyecto

> Esta guía es para el equipo de **desarrollo/IT** de la empresa que contrata el curso
> **AI-Powered QA**, no para el alumno. El alumno (Analista QA) no tiene acceso al repositorio del
> producto — por eso este documento existe: ustedes generan el contexto que el QA necesita para
> refinar historias y testear con criterio, sin darle acceso al código ni a la base de datos.

## Por qué esto existe

El Módulo 1a del curso enseña al QA a **leer e interpretar** un mapa de negocio del producto
(entidades de datos + contratos de API), no a generarlo. La generación la hace el equipo dev,
porque requiere acceso que el QA no tiene: código fuente (backend/frontend), base de datos, y el
spec OpenAPI.

Si este contexto no existe o está desactualizado, el QA va a refinar historias y diseñar pruebas
sobre suposiciones en vez de sobre cómo el sistema realmente funciona — el mismo problema que el
curso busca resolver, solo que trasladado río arriba.

## Qué tienen que generar

Cinco documentos, todos dentro de `.context/business/` en el repo del producto. El QA les va a
pedir los cinco juntos, en una sola solicitud — no en pedidos separados:

| Archivo | Qué contiene | Quién lo consume |
|---|---|---|
| `business-data-map.md` | Entidades de negocio, relaciones, flujos, máquinas de estado, procesos automáticos, integraciones externas | Módulo 1a, 1 y 2 del curso |
| `domain-glossary.md` | Terminología técnica↔negocio, significado de cada enum, reglas de negocio con ejemplo Given/When/Then, estados por entidad, labels de UI | Módulo 1a, 1 y 2 del curso — es la referencia que el QA usa para fundamentar "esta AC es ambigua" |
| `business-personas-map.md` | Roles/personas del sistema, matriz de permisos, cómo conseguir una cuenta de prueba por rol, flujos críticos y edge cases por persona | Módulo 1a, 1 y 2 del curso — le dice al QA con qué rol probar qué en el ambiente de QA |
| `business-feature-map.md` | Catálogo de funcionalidades, matriz CRUD por entidad, inventario de endpoints y componentes de UI, integraciones de terceros, feature flags, matriz de riesgo para priorizar testing | Módulo 1a y 1 del curso |
| `business-api-map.md` | Modelo de permisos/auth, journeys de negocio críticos trazados como llamadas a la API, arquitectura detrás de la API, integraciones externas en el borde de la API | Módulo 1a, 1 y 2 del curso |

Estos son artefactos **regenerativos**: no se escriben a mano, se generan con ayuda de IA a partir
de fuentes vivas (DB, código, spec OpenAPI, historial de git, i18n). Cada corrida los recalcula.

`business-feature-map.md` es distinto a los otros en una cosa importante: **no alcanza con DBHub
MCP + OpenAPI MCP**. La mitad de sus fuentes (páginas/formularios de frontend, feature flags,
`package.json`, `git log`) no tienen sustituto por MCP — necesitan lectura directa del código, algo
que ya tenés cubierto por el prerrequisito de acceso al repo de más abajo.

### Por qué importa correrlos en este orden exacto

No es una formalidad. Cada documento reusa lo que generó el anterior — mismos nombres de entidad,
mismos roles, mismo vocabulario — en vez de re-derivarlo desde cero cada vez. El orden es:
`data.md → glossary.md → personas.md → features.md → api.md`. Si lo salteás o lo corrés en otro
orden, cada prompt igual produce un archivo, pero:

- `glossary.md` sin `data.md` previo re-descubre entidades en vez de profundizar las que ya
  documentaste — trabajo duplicado.
- `personas.md` sin `glossary.md` nombra funcionalidades con vocabulario propio, que puede no
  coincidir con el que usa `features.md` después — el QA termina viendo el mismo concepto con dos
  nombres distintos en dos documentos.
- `features.md` sin `personas.md` adivina roles sueltos ("admin", "administrador", "usuario admin")
  en vez de citar las personas ya nombradas — inconsistencia que el QA nota y que resta confianza
  al resto del contexto.
- `api.md` sin los 4 anteriores nombra su propio modelo de permisos desde cero, que puede
  contradecir la matriz de permisos de `personas.md`.

El costo de generarlos en orden es cero (es el mismo prompt, en otro momento). El costo de no
hacerlo es que el QA recibe 5 documentos que hablan de las mismas entidades y roles con nombres
distintos — exactamente la ambigüedad que este bundle existe para eliminar.

## Prerrequisitos (una sola vez, del lado dev)

- Acceso de lectura al repositorio (backend + frontend).
- Credenciales de solo lectura a la base de datos del producto.
- URL o archivo del spec OpenAPI del producto.
- Un asistente de IA instalado (Claude Code CLI o Codex CLI)
- **DBHub MCP** y **OpenAPI MCP** configurados en la máquina de quien genera el contexto (estos
  MCPs SÍ los necesita el dev, no el QA — setup que haría el alumno en caso de tener acceso al repositorio).

### DBHub MCP (base de datos)

**Paso 1:** creá `dbhub.toml` en la raíz del repo (referencias a variables, no credenciales — se
puede commitear):
```toml
[[sources]]
id = "primary"
type = "${DBHUB_TYPE}"
host = "${DBHUB_HOST}"
port = "${DBHUB_PORT}"
database = "${DBHUB_DATABASE}"
user = "${DBHUB_USER}"
password = "${DBHUB_PASSWORD}"
sslmode = "require"
```

**Paso 2:** agregá a tu `.env` (nunca se commitea):
```bash
DBHUB_TYPE=postgres            # postgres | mysql | sqlserver | sqlite | mariadb
DBHUB_HOST=tu-host-de-base-de-datos
DBHUB_PORT=5432
DBHUB_DATABASE=nombre-de-tu-base
DBHUB_USER=tu-usuario-de-solo-lectura
DBHUB_PASSWORD=tu-password
```

**Paso 3:** configurá el MCP (`.mcp.json` para Claude Code, `.codex/config.toml` para Codex):
```json
{
  "mcpServers": {
    "dbhub": {
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub@latest", "--config", "dbhub.toml"]
    }
  }
}
```

### OpenAPI MCP (contratos de API)

**Paso 1:** agregá a tu `.env`:
```bash
API_BASE_URL=https://tu-ambiente-de-prueba.empresa.com/api
OPENAPI_SPEC_PATH=https://tu-ambiente-de-prueba.empresa.com/openapi.json
```

**Paso 2:** configurá el MCP:
```json
{
  "mcpServers": {
    "openapi": {
      "command": "npx",
      "args": ["-y", "@ivotoby/openapi-mcp-server", "--tools", "dynamic"],
      "env": {
        "API_BASE_URL": "${API_BASE_URL}",
        "OPENAPI_SPEC_PATH": "${OPENAPI_SPEC_PATH}"
      }
    }
  }
}
```

> El flag `--tools dynamic` es obligatorio. Ambos MCPs son de solo lectura — no modifican nada.

## Cómo generar los documentos

No necesitás instalar ninguna skill ni tener el framework de IA del curso en tu repo. Usá los 5
archivos de prompt planos que están en `contexto-dev/` (junto a esta guía): `contexto-dev/data.md`,
`contexto-dev/glossary.md`, `contexto-dev/personas.md`, `contexto-dev/features.md`,
`contexto-dev/api.md`. Son las instrucciones completas para tu asistente de IA — copiá el
contenido completo de cada uno y pegaselo a tu asistente, uno por vez, **en ese orden** (el orden
importa — ver §Por qué importa correrlos en este orden exacto más arriba). El `README.md` de esa
carpeta explica el procedimiento paso a paso.

Los 5 documentos deben incluir una sección `## Discovery Gaps` con lo que no se pudo verificar —
mejor admitir un vacío que inventar un dato.

**La primera vez** que corras esto, si los archivos no existen, se crean directo. **Las
siguientes veces**, revisá el diff contra la versión anterior antes de sobreescribir — un cambio
grande sin explicación es señal de que algo en el análisis se desvió.

> **Recomendación — un prompt a la vez, con ventana de contexto limpia.** Estos prompts son
> pesados: le piden a la IA que lea código, base de datos y documentación completos y sintetice un
> documento entero en una sola pasada. Correr los 5 seguidos en la misma conversación (sin resetear
> contexto entre uno y otro) degrada la calidad del análisis a medida que la ventana se llena —
> abrí una conversación nueva por cada prompt. Además, usá un modelo de IA robusto, no uno liviano
> optimizado para respuesta rápida: por ejemplo Claude Sonnet 5, e idealmente Claude Opus 5 si
> tenés acceso. Un modelo liviano tiende a resumir de más y a saltear fuentes que estos prompts
> piden agotar (DB, código, i18n).

## Cadencia obligatoria: cada sprint (máximo 15 días)

**Esto no es opcional para 4 de los 5 documentos.** El QA que toma el curso trabaja exclusivamente
a partir de estos documentos — no tiene otra forma de saber cómo funciona el sistema por dentro. Si
el producto cambió (nuevas entidades, endpoints, funcionalidades, reglas de negocio, flujos) y el
contexto no se actualizó:

- El QA va a refinar historias contra un modelo de datos que ya no existe.
- Los criterios de aceptación que la IA ayude a mejorar van a asumir comportamiento viejo.
- Las pruebas manuales van a validar journeys que cambiaron o desaparecieron.
- La matriz CRUD y el inventario de funcionalidades van a mostrar features que ya no existen (o
  van a faltar las nuevas).
- Bugs reales van a pasar desapercibidos porque el punto de referencia está mal.

**Regla:** regenerar `business-data-map.md`, `domain-glossary.md`, `business-feature-map.md` y
`business-api-map.md` al menos una vez por sprint (cada 15 días), idealmente al cierre de sprint o
al arranque del siguiente, antes de que el QA empiece su ciclo de refinamiento/testing.

**Excepción — `business-personas-map.md` no sigue esta cadencia.** Los roles y permisos de un
sistema cambian mucho menos seguido que sus entidades o endpoints. Regenerá este documento
**solo cuando se agregue o modifique un rol/permiso** — no hace falta correrlo cada sprint si los
roles no cambiaron. Esto es a propósito: no queremos que el equipo dev sienta este documento como
una carga semanal extra sin motivo.

### Marcá la fecha de vigencia

Agregá una línea al tope de cada documento:
```markdown
> Última verificación: YYYY-MM-DD
```
Así el QA puede confirmar de un vistazo si lo que tiene en mano está dentro de la ventana de 15
días, sin tener que preguntarle al equipo dev cada vez.

## Cómo entregarlo al QA (sin darle acceso al repo)

El QA no clona el repositorio. Elegí un canal estable y usalo siempre el mismo:

- Carpeta compartida (Drive, SharePoint, Confluence) con los archivos `.md` actualizados, mismo
  link cada sprint.
- Adjunto por el canal interno que ya usen para coordinar con QA (Slack, email, Jira).

Evitá mandarlo "cuando se acuerden" por canales ad-hoc — si el link/ubicación cambia cada vez, el
QA termina trabajando con una copia vieja porque no sabe que hay una nueva.

## Checklist por sprint

- [ ] Corrí `contexto-dev/data.md` → revisé el diff → aprobé
- [ ] Corrí `contexto-dev/glossary.md` → revisé el diff → aprobé
- [ ] Corrí `contexto-dev/personas.md` — **solo si cambió un rol/permiso este sprint** → revisé el diff → aprobé
- [ ] Corrí `contexto-dev/features.md` → revisé el diff → aprobé
- [ ] Corrí `contexto-dev/api.md` → revisé el diff → aprobé
- [ ] Agregué/actualicé la línea `> Última verificación: YYYY-MM-DD` en los archivos que corrí
- [ ] Subí los archivos actualizados al canal compartido con el QA
- [ ] Avisé al QA que hay contexto nuevo (no asumas que va a revisar el link por su cuenta)

## Responsable

Definan una persona (tech lead o dev designado) responsable de correr este checklist cada sprint.
Sin dueño explícito, esto se salta la primera vez que hay presión de entrega — y ahí es exactamente
cuando el QA más lo necesita actualizado.
