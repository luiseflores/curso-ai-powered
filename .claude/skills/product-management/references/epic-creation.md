# Workflow C: Crear una épica

> Crear una épica en Jira y descomponerla en 3 a 8 historias listas para refinar.

Se llega acá de dos formas:

- El usuario pide "creá una épica". Clasificá primero con `add-feature.md` Paso 2: si resulta
  Nivel 1, es una historia y sigue por B.
- `add-feature.md` clasificó la idea como Nivel 2, o como Nivel 3 y el usuario eligió una de las
  épicas propuestas.

---

## Entradas: leelas primero, en este orden

1. `.claude/jira-fields.json`: IDs de los custom fields de esta skill. Si falta alguno, frená
   (ver "Antes de cualquier workflow" en `SKILL.md`).
2. `.claude/jira-required.yaml`: statuses de Story y Épica.
3. Clave del proyecto: sección `## Proyecto Jira` de `CLAUDE.md`.
4. Las épicas existentes en Jira (búsqueda de épicas del proyecto con `[ISSUE_TRACKER_TOOL]`),
   para no crear una que ya existe.

---

## Qué es una épica

Un bloque de trabajo que entrega una capacidad completa a la persona y es demasiado grande para
una sola historia. Agrupa 3 a 8 historias bajo un mismo padre en Jira y responde "¿para qué
construimos esto?". Si no la podés describir en dos oraciones, probablemente son dos épicas.

## Nombre de la épica

**Frase nominal de módulo**, 2 a 5 palabras, sin pipe ni verbo, en español (`Carrito y pagos`,
`Gestión de usuarios`). Describe la capacidad, no cómo se construye (`Pagos`, no
`Integración con Stripe`). Usá el nombre de módulo que use el usuario.

---

## Step 1: Descomponer en historias

Si te faltan datos, preguntale al usuario (máximo 5 preguntas por ronda). Después
armá la hoja:

```markdown
## Épica: <nombre>

1. Como <persona>, quiero <acción>, para <beneficio>
2. Como <persona>, quiero <acción>, para <beneficio>
3. Como <persona>, quiero <acción>, para <beneficio>
```

Pasá un INVEST liviano por cada historia:

| Criterio | Chequeo |
|---|---|
| **I**ndependiente | ¿Se puede completar sin esperar a sus historias hermanas? |
| **N**egociable | ¿Dice el *qué* y deja abierto el *cómo*? |
| **V**aliosa | ¿El "para…" es un beneficio claro para la persona o el negocio? |
| **E**stimable | ¿Hay información suficiente para estimarla? |
| **P**equeña | ¿Entra en un sprint? Si no, dividila. |
| **T**esteable | ¿Sus AC se pueden verificar? |

Revisá también que dos historias no compartan un ítem de Alcance (I4). Si comparten, ofrecé:
**(a)** dejar el ítem en una sola, **(b)** extraer una historia compartida de la que ambas
dependan, o **(c)** aceptarlo si es intencional (raro, con el motivo explicado).

## Step 2: Borrador y OK

Armá el borrador completo:

- **La épica:** título, prioridad (Alta, Media o Baja) y descripción con esta plantilla:

  ```markdown
  ## Objetivo
  <2-3 oraciones: qué capacidad entrega y para quién>

  ## Valor de negocio
  <por qué importa ahora>

  ## Historias
  <se completa con las claves a medida que se crean>
  ```

  La descripción de la épica no repite AC, Alcance ni Fuera de alcance de sus historias: eso vive
  en los campos de cada historia (`description-custom-field-dedup.md`).

- **Cada historia:** con el contrato de "Campos de la historia" de `add-feature.md`.

Pasá el pre-flight de voz y formato de `SKILL.md` por cada historia y mostrá todo junto.
Esperá aprobación explícita: sin OK no se escribe nada.

## Step 3: Crear la épica

1. Pedile a `[ISSUE_TRACKER_TOOL]` que cree un issue de tipo Epic (operación "Crear issue" de
   `jira-operations.md`) con el título, la descripción (Markdown convertido a ADF con la receta de
   la skill `acli`) y la prioridad.
2. Capturá la clave que asigna Jira (`CLAVE-NUM`).
3. La épica queda en su status inicial (`BACKLOG`). No se transiciona.

## Step 4: Crear las historias

Por cada historia, igual que `add-feature.md` Paso 5, con la épica del Step 3 como padre.
Capturá cada clave. Si escribís varios custom fields por MCP, uno por llamada o pre-convertidos
(`jira-publishing-gotchas.md`).

Con las claves, completá la sección `## Historias` de la descripción de la épica.

## Step 5: Transicionar las historias

Pasá cada historia a `REFINAMIENTO`. Si el workflow no ofrece esa transición, avisá y dejala en
su status inicial.

## Step 6: Dependencias

Seguí `dependency-linking.md` (búsqueda activa, creación y verificación de links), aunque el
resultado sea "sin dependencias" (I8, I18).

## Step 7: Leer de vuelta

Leé cada historia igual que `add-feature.md` Paso 8, y confirmá que no hay un ítem de Alcance
repetido entre historias. Si aparece uno, mostrá
`overlap_alert: <CLAVE-A> vs <CLAVE-B> en el ítem "<texto>"` y ofrecé las opciones del Step 1.

---

## Resultado

- Épica creada en Jira, en `BACKLOG`, con sus historias listadas en la descripción.
- 3 a 8 historias creadas, colgadas de la épica, en `REFINAMIENTO`.
- Dependencias publicadas como links (o "sin dependencias").
- Sin ítems de Alcance repetidos entre historias.

## Referencias

- `add-feature.md`: clasificación (Paso 2) y contrato de cada historia ("Campos de la historia").
- `jira-operations.md`: qué herramienta usar para cada operación en Jira.
- `dependency-linking.md`: búsqueda activa y links.
- `description-custom-field-dedup.md`: qué va en la descripción y qué no.
- `jira-publishing-gotchas.md`: trampas del formato ADF.
