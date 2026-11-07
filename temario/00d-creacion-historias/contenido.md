# Contenido — Tema 00d: Crear épicas e historias en Jira

> Orden intercalado: cada concepto va seguido de la acción que lo pone en uso.
>
> **Modelo de práctica:** la skill `product-management` y su línea en `CLAUDE.md` llegan con
> `git pull` al iniciar el tema. En clase el alumno trabaja sobre el **producto de práctica**:
> Practice Software Testing, "Toolshop" (<https://practicesoftwaretesting.com>), una tienda
> online de herramientas con roles `user` (cliente) y `admin`. La skill no tiene contexto de
> negocio cargado: entrevista por todo y el alumno responde navegando la app. Las historias se
> crean en el proyecto Jira de práctica. En su empresa repite el flujo con su producto real, en
> su repo de trabajo.

## 1. Anatomía de una historia de usuario

**Qué es:** una necesidad de un usuario, escrita para que todo el equipo entienda lo mismo.

- **Título:** `{Funcionalidad} | {Acción}`. Ej: `Carrito | Agregar producto al carrito`.
- **Historia (en la descripción):** "**Como** cliente registrado, **quiero** agregar una
  herramienta al carrito, **para** comprarla junto con el resto de mi pedido". Un verbo y un
  objeto: si aparece un "y", son dos historias. El "para" es un beneficio, no la misma función
  repetida.
- **Reglas de negocio, Alcance, Fuera de alcance, Criterios de aceptación:** cada uno en su
  campo, nunca copiados en la descripción.
- **Criterios de aceptación:** mínimo 3 escenarios en Gherkin (1 camino feliz, 1 error, 1 borde),
  con datos concretos.

```gherkin
Escenario: Solo un Thor Hammer por cliente
  Dado un carrito con 1 "Thor Hammer" de $11.14
  Cuando intento agregar otro "Thor Hammer"
  Entonces el carrito sigue con 1 "Thor Hammer"
  Y veo un aviso de que solo se permite uno por cliente
```

> Regla real de Toolshop: la descripción del producto dice "only one Thor Hammer is permitted per
> customer". El texto exacto del aviso se confirma en la app antes de cerrar el criterio.

- **Voz de negocio:** nada de endpoints, códigos HTTP ni tablas. Si cambia la tecnología y el
  criterio sigue siendo cierto, está bien escrito.
- **Story Points vacíos:** estima el equipo que construye, no quien escribe la historia.

## 2. Épica vs historia

- Historia: entra en un sprint y en una épica que ya existe.
- Épica: 3 a 8 historias de un mismo módulo. Título nominal, sin verbo (`Carrito y checkout`).
- Más de 8 historias o varias épicas: es demasiado grande, hay que partirlo antes.

## 3. Acción: crear tu primera épica e historia con `product-management`

**Qué es:** la skill del repo que crea épicas e historias en Jira.

**Cómo trabaja:**
1. Revisa `.claude/jira-fields.json` (si falta un ID, frena).
2. Te pide que describas la necesidad con tus palabras.
3. Te entrevista por lo que necesita (rol, reglas, alcance, escenarios), máximo 5 preguntas por
   ronda.
4. Te muestra el borrador. Nada se escribe sin tu OK.
5. Convierte los campos largos a ADF con Bun y crea en Jira con `acli`.

**Acción** (con `bun run claude`):
1. `creá una épica para Favoritos` (o el módulo de Toolshop que elijas). La skill propone la épica
   **y sus 3 a 8 historias** en el mismo borrador. Las respuestas salen de navegar la app.
2. `creá una historia para <necesidad de Favoritos que no esté entre las anteriores>` (ej: ordenar
   mis favoritos por precio). La skill la cuelga de la épica que ya existe.

**Verificación:**
1. La épica y sus historias existen en Jira; cada historia es hija de la épica y está en
   `REFINAMIENTO`.
2. Los criterios de aceptación se ven como bloque de código Gherkin.
3. La descripción no repite AC, Alcance ni Fuera de alcance.
4. El "Como" nombra un rol concreto (cliente registrado, admin), nunca "el usuario".

**En tu empresa:** mismo flujo con tu producto real, en tu repo de trabajo con la receta del 00c
aplicada (campos, statuses, IDs y clave de tu Jira).

## 4. Dependencias entre historias: Blocks y Relates

- **Blocks / is blocked by:** dependencia dura, una historia no puede empezar sin la otra.
- **Relates:** solo informativa, no indica orden.
- La skill te pregunta si la historia nueva depende de otra y crea el link `Blocks`. `acli`
  invierte los flags `--out`/`--in`, por eso la skill verifica la dirección después de crearlo.
- Ambos tipos vienen con Jira: no se crean.

## Cierre del tema

Al terminar este tema, el alumno: reconoce una historia bien escrita (título, Como/Quiero/Para,
campos separados, 3 escenarios Gherkin en voz de negocio), distingue épica de historia, y creó
con `product-management` su primera épica, sus historias y una historia extra de Toolshop en el
Jira de práctica, respondiendo la entrevista desde la app y sabiendo repetirlo en su empresa.
