# Guión de diapositivas — Tema 00d: Crear épicas e historias en Jira

> Mismo orden intercalado que `contenido.md`. Cada diapositiva de concepto lleva 3-5 bullets de
> repaso, no un párrafo largo. Cada diapositiva de "acción" se dicta con la máquina del alumno
> abierta. Ejemplos de Toolshop (<https://practicesoftwaretesting.com>) verificados contra su API
> el 2026-11-07: categorías, marcas, "Thor Hammer" ($11.14, "only one Thor Hammer is permitted per
> customer") y "Long Nose Pliers" (sin stock).

## Diapositiva 1 — Portada

**Título en pantalla:** "Tema 00d — Crear épicas e historias en Jira"

**Guión:**
- Hoy creamos la primera épica y sus historias en el Jira de práctica, con la skill
  `product-management`.
- Antes vemos qué hace buena a una historia: título, Como/Quiero/Para, campos separados y
  criterios de aceptación en Gherkin.
- La skill les hace una entrevista y ustedes responden navegando Toolshop.
- La skill llega con `git pull`: la usan, no la crean.

## Diapositiva 2 — El producto de práctica: Toolshop

**Guión:**
- Practice Software Testing, "Toolshop": una tienda online de herramientas hecha para practicar
  testing. Es real, la pueden abrir ahora.
- Roles: `user` (cliente que compra) y `admin` (gestiona productos, categorías y pedidos).
- Catálogo: 3 categorías raíz (Hand Tools, Power Tools, Other) con 16 subcategorías y 2 marcas
  (ForgeFlex Tools, MightyCraft Hardware).
- Módulos para historias: catálogo con filtros y búsqueda, detalle de producto, favoritos,
  carrito, checkout, facturas, mensajes de contacto, alquileres.
- Las respuestas de la entrevista de hoy salen de navegar la app. En su empresa, de su producto.

## Diapositiva 3 — Anatomía: título y Como/Quiero/Para

**Guión:**
- Historia = una necesidad de un usuario, escrita para que todo el equipo entienda lo mismo.
- Título `{Funcionalidad} | {Acción}`: `Carrito | Agregar producto al carrito`.
- En la descripción: "Como cliente registrado, quiero agregar una herramienta al carrito, para
  comprarla junto con el resto de mi pedido".
- Un verbo y un objeto: si aparece un "y", son dos historias.
- El "para" es un beneficio, no la misma función repetida ("para tenerla en el carrito" no
  aporta nada).

## Diapositiva 4 — Anatomía: cada cosa en su campo

**Guión:**
- Reglas de negocio, Alcance, Fuera de alcance y Criterios de aceptación: cada uno en su campo
  Párrafo (los que crearon en el 00c).
- La descripción solo lleva la historia: nunca copia el contenido de esos campos.
- Por qué: si el AC está en dos lugares, alguien corrige uno y el otro queda viejo.
- Ejemplo: regla "solo un Thor Hammer por cliente" va en Reglas de negocio; "comprar como
  invitado" puede ir en Fuera de alcance.
- Story Points vacíos: estima el equipo que construye, no quien escribe la historia.

## Diapositiva 5 — Criterios de aceptación en Gherkin

**Guión:**
- Mínimo 3 escenarios: 1 camino feliz, 1 error, 1 borde. Con datos concretos, nunca "un
  producto".
- Gherkin: Escenario / Dado (contexto) / Cuando (acción) / Entonces (resultado) / Y.
- Ejemplo de borde con una regla real de Toolshop: la descripción del Thor Hammer dice "only one
  Thor Hammer is permitted per customer".
- Ejemplo de error: "Long Nose Pliers" está sin stock en la app.
- Gotcha: el texto exacto de un mensaje se confirma en la app antes de cerrar el criterio. No se
  inventa.

## Diapositiva 6 — Voz de negocio

**Guión:**
- Los criterios describen lo que ve y hace el usuario, no cómo está construido.
- Nada de endpoints, códigos HTTP, tablas ni nombres de campos de la base.
- Prueba rápida: si cambia la tecnología y el criterio sigue siendo cierto, está bien escrito.
- Comparación en pantalla: "POST /carts devuelve 422" contra "veo un aviso de que solo se
  permite uno por cliente".

## Diapositiva 7 — Épica vs historia

**Guión:**
- Historia: entra en un sprint y cuelga de una épica que ya existe.
- Épica: 3 a 8 historias de un mismo módulo. Título nominal, sin verbo: `Favoritos`,
  `Carrito y checkout`.
- Más de 8 historias, o algo que pide varias épicas: es demasiado grande, se parte antes.
- Ejemplo: épica `Favoritos` con `Favoritos | Agregar producto a favoritos`,
  `Favoritos | Quitar producto de favoritos`, `Favoritos | Ver mis favoritos`.

## Diapositiva 8 — La skill `product-management`

**Guión:**
- Skill del repo que crea épicas e historias en Jira.
- 1: revisa `.claude/jira-fields.json`; si falta un ID, frena.
- 2: te pide que describas la necesidad con tus palabras.
- 3: te entrevista por lo que necesita (rol, reglas, alcance, escenarios), máximo 5 preguntas por
  ronda.
- 4: te muestra el borrador completo. Nada se escribe sin tu OK.
- 5: convierte los campos largos a ADF con Bun y crea en Jira con `acli`.

## Diapositiva 9 — Acción: crear tu primera épica

**Guión:**
- Abrí Claude con `bun run claude` (las variables del `.env` cargadas).
- Pedile: `creá una épica para Favoritos` (o el módulo de Toolshop que elijas).
- Respondé la entrevista navegando la app: qué puede hacer el cliente en favoritos, qué pasa sin
  sesión iniciada.
- Revisá el borrador: la épica con título nominal y las 3 a 8 historias que propone. Recién ahí
  das el OK.
- Verificación: la épica y sus historias existen en el Jira de práctica, en `REFINAMIENTO`.

## Diapositiva 10 — Acción: crear tu primera historia

**Guión:**
- Pedile una historia que no esté entre las de la épica: `creá una historia para ordenar mis
  favoritos por precio`.
- La skill propone la épica padre y te pregunta si la historia depende de otra.
- En el borrador revisá: título `{Funcionalidad} | {Acción}`, Como/Quiero/Para, 3 escenarios,
  campos separados.
- Verificación 1: la historia es hija de la épica y está en `REFINAMIENTO`.
- Verificación 2: los AC se ven como bloque de código Gherkin y la descripción no repite AC,
  Alcance ni Fuera de alcance.
- Verificación 3: el "Como" nombra un rol concreto (cliente registrado, admin), nunca "el
  usuario".

## Diapositiva 11 — Dependencias: Blocks y Relates

**Guión:**
- Blocks / is blocked by: dependencia dura, una historia no puede empezar sin la otra.
- Relates: solo informativa, no indica orden.
- Ejemplo: `Favoritos | Ver mis favoritos` is blocked by `Favoritos | Agregar producto a
  favoritos`.
- La skill te pregunta si la historia nueva depende de otra y crea el link `Blocks`. Ambos tipos
  vienen con Jira: no se crean.
- Gotcha: `acli` invierte los flags `--out`/`--in`, por eso la skill verifica la dirección
  después de crear el link.

## Diapositiva 12 — En tu empresa

**Guión:**
- Mismo flujo con tu producto real, en tu repo de trabajo, no en el clon del curso.
- Antes: la receta del 00c aplicada a tu Jira (campos Párrafo, statuses, IDs en
  `jira-fields.json`, clave en `CLAUDE.md`).
- La entrevista pregunta todo en cada historia: tené a mano al PO o la app de tu producto.
- Más adelante en el curso ves cómo darle contexto de negocio a la skill para que pregunte menos.

## Diapositiva 13 — Cierre y checklist

**Guión:**
- Reconocen una historia bien escrita: título, Como/Quiero/Para, campos separados, 3 escenarios
  Gherkin en voz de negocio.
- Distinguen épica de historia.
- Crearon con `product-management` su primera épica, sus historias y una historia extra de
  Toolshop en el Jira de práctica.
- Saben repetirlo en su empresa, con su Jira y su producto.
- La próxima clase: mapas de datos y API (Tema 00e).
