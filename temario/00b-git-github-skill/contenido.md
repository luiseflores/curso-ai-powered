# Contenido — Tema 00b: Git, GitHub y Skill

> Orden intercalado, igual que en el Tema 00a: cada concepto va seguido de la acción práctica
> que lo pone en uso. La práctica de este tema (crear y clonar el repositorio) es la que
> inicializa `.git` en `curso-ai-powered/` por primera vez — confirmado explícitamente por el
> instructor en `docs/superpowers/specs/2026-09-12-curso-ai-powered-tema-00b-design.md`.

## 1. Terminal — básicos básicos

**Qué es:** los tres comandos mínimos para moverte por carpetas desde la terminal. El resto de
comandos de terminal (`mkdir`, `rm`, `mv`, etc.) queda como tarea de investigación del alumno —
acá solo vemos lo indispensable para lo que sigue.

- `cd <carpeta>` — entrar a una carpeta.
- `ls` — listar el contenido de la carpeta actual.
- `cd ..` — subir un nivel (volver a la carpeta contenedora).

**Acción:** navegar desde la carpeta de usuario hasta la carpeta de trabajo del curso usando
solo estos tres comandos.

**Verificación:** `ls` muestra el contenido esperado de la carpeta de trabajo del curso.

## 2. Qué es Git

**Qué es:** un sistema de control de versiones — un programa que guarda el historial de cambios
de una carpeta de proyecto, para poder ver qué cambió, cuándo, y volver atrás si hace falta.
Corre en tu máquina, local.

**Por qué importa:** sin Git, un proyecto solo tiene su estado actual — perdés el historial y no
podés colaborar con otras personas sobre los mismos archivos de forma ordenada.

## 3. Qué es GitHub

**Qué es:** un servicio en la nube que aloja repositorios Git. Git es el programa; GitHub es
donde tu repositorio vive en internet, para que vos (u otras personas) lo descarguen desde
cualquier máquina.

**Diferencia clave:** Git funciona sin GitHub (podés versionar una carpeta local sin subirla a
ningún lado). GitHub no funciona sin Git (es un lugar para alojar repositorios Git, no un
sistema de versionado en sí mismo).

## 4. Qué es un repositorio

**Qué es:** una carpeta de proyecto que Git está versionando — tiene una subcarpeta oculta
`.git/` donde vive todo el historial de cambios. "Repo" es la forma corta de decir lo mismo.

- `.git/` es la base de datos real del repositorio (commits, historial, configuración). Nunca se
  edita a mano — se crea sola con `git init` o `git clone`.
- Borrar `.git/` no borra tus archivos de trabajo, pero sí todo el historial — la carpeta deja de
  ser un repositorio.
- `.gitignore` es distinto: un archivo de texto normal (NO una carpeta oculta) que le dice a Git
  qué archivos/carpetas **nunca** versionar — ni con `git add`, ni aparecen en `git status`.
- Ejemplo real de este curso (`.gitignore` de tu proyecto): `.env` (credenciales — las vas a ver
  en detalle la próxima clase) y `node_modules/` (dependencias descargadas, no código propio).
- Por qué importa: sin `.gitignore`, un `git add .` subiría credenciales o carpetas gigantes de
  dependencias al repositorio — un error común y evitable.

## 5. Comandos git de trabajo diario

**Qué son:** los seis comandos que vas a usar todos los días para mantener tu copia local
sincronizada con GitHub y subir tus propios cambios. No vemos `branch` ni `merge` en este tema —
quedan para cuando el curso te pida trabajar con ramas.

- `git fetch` — trae del servidor (GitHub) qué cambió, sin tocar tus archivos todavía.
- `git status` — muestra el estado actual: qué cambió, qué falta subir/bajar.
- `git pull` — trae los cambios del servidor y los aplica a tu copia local.
- `git add .` — marca todos los archivos que cambiaste para que entren en el próximo commit.
- `git commit -m "<mensaje>"` — guarda esos cambios marcados como un punto en el historial, con
  un mensaje corto que explica qué hiciste.
- `git push` — sube tus cambios locales (ya commiteados) al servidor.

**Verificación:** correr `git status` dentro de un repositorio clonado y leer la salida en voz
alta, explicando qué dice. Después de un `git add .`, `git status` muestra los archivos en la
sección "Changes to be committed" — antes de eso están en "Changes not staged for commit".

## 6. Acción: crear tu repositorio en GitHub y clonarlo

**Qué es "clonar":** descargar una copia completa de un repositorio de GitHub a tu máquina, con
todo su historial incluido.

**Acción:**
1. En GitHub, crear un repositorio nuevo (privado, vacío, sin README inicial).
2. Copiar la URL del repo (botón "Code" → HTTPS).
3. En la terminal, en la carpeta donde va a vivir tu trabajo del curso: `git clone <URL>`.

Este clone es tu `curso-ai-powered/` — a partir de acá, esa carpeta tiene `.git` inicializado.

**Verificación:** `cd` dentro de la carpeta clonada, correr `git status` y confirmar que dice
"nothing to commit, working tree clean" (repo vacío recién clonado).

**Acción — tu primer commit:** con el repo ya clonado, hacer un cambio chico (ej. agregar una
línea a `README.md`) y correr el flujo completo: `git add .` → `git commit -m "<mensaje
corto>"` → `git push`. Este es el flujo mínimo completo del tema: editar → `add` → `commit` →
`push`.

**Verificación:** confirmar en GitHub (en el navegador) que el commit y el cambio llegaron al
repositorio remoto.

## 7. Ingeniería de prompt

**Qué es:** la práctica de escribir instrucciones (prompts) claras y con contexto suficiente
para que el asistente de IA dé el resultado que necesitás, en vez de una respuesta genérica o
equivocada.

**Las 4 piezas de un buen prompt** (siempre las mismas, sin importar el área):
- **Tarea:** qué querés que haga, con un verbo concreto.
- **Contexto:** qué necesita saber el asistente para no inventar (archivos, datos, restricciones
  del dominio).
- **Restricciones:** qué NO tiene que hacer, o los límites del resultado.
- **Formato:** cómo tiene que verse el output (lista, tabla, código, etc.).

**Ejemplo — prompt vago vs. prompt con contexto**, pensando en pedirle algo al asistente sobre
el proyecto:

- Vago: "dame el contexto del proyecto."
- Con contexto: "corré la skill `project-context` en modo `test-plan` sobre los documentos que
  ya tengo en `.context/business/`, y contame qué journeys de negocio identificó."

**Por qué importa:** el segundo prompt le dice al asistente QUÉ herramienta usar, CÓMO (qué
modo), y SOBRE QUÉ datos — mucho menos margen para que invente o pida aclaraciones de más.

**Consejos para aplicar ingeniería de prompt:**
1. **Definí el resultado observable antes de escribir el prompt.** Si no podés describir cómo se
   ve un output perfecto, no vas a poder pedirlo ni evaluarlo.
2. **Las 4 piezas siempre son las mismas: tarea, contexto, restricciones, formato.** Dominás esto
   y lo aplicás en cualquier área, sin importar cuánto sepas del tema.
3. **El "qué NO hacer" pesa tanto como el "qué hacer".** Frenar errores típicos y evitar que el
   modelo invente es tan importante como pedir bien.
4. **Usá al modelo para investigar antes de pedirle que ejecute.** No necesitás dominar el
   dominio: necesitás saber preguntar qué te falta saber.
5. **Iterá y guardá lo que funciona.** Un prompt no se perfecciona a la primera; se prueba, se
   ajusta, y se convierte en plantilla reusable para la próxima vez.

**Nota:** ese último consejo (guardar lo que funciona como plantilla reusable) es exactamente lo
que resuelve una skill. Lo vemos a continuación.

## 8. Qué es una skill y su estructura

**Qué es:** una skill es un paquete de instrucciones que le enseña al asistente de IA a hacer
una tarea específica de una forma reproducible — en vez de explicarle todo de cero cada vez, el
asistente lee la skill cuando la necesita.

**Prompt vs. skill — la diferencia:** un prompt es de un solo uso — lo escribís, lo mandás, y se
pierde cuando cerrás la conversación. Una skill toma ese mismo trabajo de ingeniería de prompt ya
afinado y lo empaqueta para reusar: se guarda en disco, persiste entre conversaciones, y el
asistente decide solo cuándo aplicarla — no hace falta explicársela de nuevo cada vez.

**Acción — mirar dos skills reales:** abrir dos carpetas de `.agents/skills/` y ver cómo están
armadas (todavía no las vamos a usar, solo miramos la forma):
- `project-context/` — genera o actualiza los mapas de negocio del producto (datos,
  funcionalidades, API) y el plan maestro de pruebas. Se regenera cada sprint, porque el producto
  cambia rápido.
- `project-discovery/` — genera el glosario de dominio y el mapa de personas/roles: el
  vocabulario base que `project-context` y el resto del curso asumen ya resuelto.
- Las dos tienen la misma forma: `SKILL.md` (qué hace, cuándo usarla) + `references/` (detalle
  por modo, para no saturar el `SKILL.md`).
- Esto es un resumen — para entender un modo a fondo hay que leer su archivo completo en
  `references/`. Es lectura de profundización tuya, no la vemos línea por línea en esta clase.

**Cómo se arma una buena skill:**
- Un skill es un paquete reutilizable, no una instrucción de un solo uso. Se guarda como carpeta
  con un `SKILL.md`, y persiste entre conversaciones — no tenés que reescribirlo cada vez.
- La descripción es la parte más crítica. Es lo único que el asistente mira para decidir si el
  skill aplica a tu pedido; si es débil o genérica, el skill nunca se activa aunque esté bien
  escrito por dentro.
- Se carga en capas, no todo de una. Primero nombre y descripción, después el cuerpo completo
  (solo si aplica), y por último recursos extra (scripts, plantillas) si hacen falta — así no
  satura el contexto.
- Instrucciones claras, en modo imperativo, con ejemplos. Un buen ejemplo de input → output vale
  más que párrafos de reglas abstractas.
- Se crea igual que un buen prompt: se prueba, se evalúa y se ajusta. La diferencia es que, una
  vez afinado, queda guardado como un activo permanente en vez de perderse al cerrar la
  conversación.

**Nota:** en este tema ya sabés QUÉ hace `project-context` y QUÉ hace `project-discovery` (el
resumen de arriba), pero no CÓMO se usan en la práctica (correr un modo, leer el diff, aprobarlo)
— eso es contenido del Tema 01a. Acá el objetivo es reconocer la forma de una skill y tener un
mapa mental de para qué sirve cada una.

## Cierre del tema

Al terminar este tema, el alumno: navega carpetas con `cd`/`ls`/`cd ..`, explica con sus propias
palabras qué es Git/GitHub/repositorio (incluyendo para qué sirven `.git/` y `.gitignore`), tiene su `curso-ai-powered/` clonado desde su propio
repositorio GitHub, corrió el flujo mínimo completo (`fetch`/`status`/`pull` para sincronizar,
`add`/`commit`/`push` para subir sus propios cambios), aplicó las 4 piezas de un
prompt para escribir uno "mejorado" comparado contra uno vago, identificó los archivos que
componen una skill y en qué se diferencia de un prompt suelto, y sabe qué hace `project-context`
y qué hace `project-discovery` (sin haberlas usado todavía).
