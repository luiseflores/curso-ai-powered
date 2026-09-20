# Contexto de negocio para el equipo Dev

Estos 5 documentos son las instrucciones que le das a tu asistente de IA (Claude Code, Codex CLI,
u otro) para que analice tu producto y redacte los 5 archivos de contexto que el equipo de QA
necesita (ver `../guia-dev-contexto.md`). No son código ni forman parte de tu repositorio — son
prompts: pegale el contenido completo de cada uno a tu asistente, uno por vez.

## Orden de generación (obligatorio)

Generá los documentos en este orden, no en otro. El orden importa en serio: cada paso reusa lo que
generó el anterior para producir un resultado más consistente — mismos nombres de entidad, mismos
roles, sin re-derivar terminología distinta en cada archivo. Saltear el orden no rompe nada, pero
sí empobrece el contexto final que recibe el QA (más ambigüedad, menos trazabilidad entre
documentos).

1. **`data.md`** → produce `.context/business/business-data-map.md`. No depende de nada — arranca
   siempre por acá.
2. **`glossary.md`** → produce `.context/business/domain-glossary.md`. Usa `business-data-map.md`
   para profundizar sus entidades en terminología técnico↔negocio, reglas y estados.
3. **`personas.md`** → produce `.context/business/business-personas-map.md`. Usa la terminología
   de `domain-glossary.md` para nombrar personas y funcionalidades de forma consistente.
4. **`features.md`** → produce `.context/business/business-feature-map.md`. Usa
   `business-data-map.md` para verificar CRUD, y `domain-glossary.md`/`business-personas-map.md`
   para nombrar entidades y roles con el vocabulario ya canonizado en vez de re-derivarlo.
5. **`api.md`** → produce `.context/business/business-api-map.md`. Usa los 4 anteriores para
   elegir mejor los recorridos de negocio críticos, nombrar el modelo de permisos de forma
   consistente, y evitar recorridos huérfanos. Por eso va último.

Si generás un archivo antes de que exista el anterior, igual funciona — pero el resultado es más
pobre (menos contexto, terminología inconsistente entre documentos). Preferí siempre el orden de
arriba.

## Cómo usarlos

1. Abrí `data.md`, copiá todo su contenido, pegaselo a tu asistente de IA con el repo del producto
   abierto (backend + frontend + acceso de solo lectura a la DB + spec OpenAPI, según los
   prerrequisitos de `../guia-dev-contexto.md`).
2. Repetí con `glossary.md`.
3. Repetí con `personas.md`.
4. Repetí con `features.md`.
5. Repetí con `api.md`.
6. Subí los 5 archivos resultantes (`.context/business/*.md`) al canal que uses para compartir con
   el equipo de QA — ver la sección "Cómo entregarlo al QA" de `../guia-dev-contexto.md`.

## Cadencia

No todos los documentos se regeneran con la misma frecuencia:

- **`data.md`, `glossary.md`, `features.md`, `api.md`** — al menos una vez por sprint (cada 15
  días), misma regla que `../guia-dev-contexto.md`, o cuando el producto cambie de forma
  significativa.
- **`personas.md`** — solo cuando se agrega o modifica un rol/permiso del sistema. No hace falta
  regenerarlo cada sprint si los roles no cambiaron.
