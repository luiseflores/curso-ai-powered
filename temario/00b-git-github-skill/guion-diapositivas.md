# Guión de diapositivas — Tema 00b: Git, GitHub y Skill

> Mismo orden intercalado que `contenido.md`. Cada diapositiva de "acción" se dicta con la
> máquina del alumno abierta — pausa para que ejecute antes de seguir.

## Diapositiva 1 — Portada

**Título en pantalla:** "Tema 00b — Git, GitHub y tu primera skill"

**Guión:** Hoy sumamos dos piezas clave: cómo versionar y compartir tu trabajo con Git y GitHub,
y cómo está armada una skill — el bloque que le enseña tareas concretas a tu asistente de IA. Al
final de la clase, tu carpeta de trabajo va a ser un repositorio real de GitHub.

## Diapositiva 2 — Terminal: básicos básicos

**Guión:** Antes de git necesitamos moverte por carpetas desde la terminal. Solo tres comandos:
`cd` para entrar a una carpeta, `ls` para ver qué hay adentro, `cd ..` para volver un nivel
atrás. El resto de comandos de terminal quedan de tarea para investigar por su cuenta.

## Diapositiva 3 — Acción: navegá con cd, ls y cd ..

**Guión:** Practiquen: entren a la carpeta de trabajo del curso con `cd`, listen su contenido
con `ls`, salgan con `cd ..` y vuelvan a entrar. Avisen si `ls` no muestra lo que esperan.

## Diapositiva 4 — ¿Qué es Git?

**Guión:** Git es un sistema de control de versiones — guarda el historial de cambios de una
carpeta de proyecto. Corre en su máquina, local, sin necesitar internet para funcionar.

## Diapositiva 5 — ¿Qué es GitHub?

**Guión:** GitHub es un servicio en la nube que aloja repositorios Git. La diferencia clave: Git
funciona sin GitHub, pero GitHub no existe sin Git — es el lugar donde tu repositorio vive en
internet.

## Diapositiva 6 — ¿Qué es un repositorio?

**Guión:** Un repositorio es una carpeta de proyecto que Git está versionando — tiene adentro
una carpeta oculta `.git/` con todo el historial. "Repo" es la forma corta de decirlo. `.git/` es
la base de datos real del repo: nunca se edita a mano, se crea sola con `clone` o `init`, y si la
borran pierden el historial (no los archivos). Distinto es `.gitignore`: un archivo de texto
normal que le dice a Git qué NUNCA versionar — en este curso, `.env` (credenciales, lo vemos en
detalle la próxima clase) y `node_modules/`. Sin `.gitignore`, un `git add .` subiría eso al
repositorio.

## Diapositiva 7 — Comandos git de trabajo diario

**Guión:** Hoy vemos seis comandos, los que van a usar todos los días: `git fetch` trae qué
cambió sin tocar nada todavía, `git status` muestra el estado actual, `git pull` trae y aplica
los cambios. `git add .` marca lo que cambiaron, `git commit -m` lo guarda en el historial con un
mensaje, y `git push` sube ese commit al servidor. No vemos `branch` hoy — eso viene cuando
trabajen con ramas.

## Diapositiva 8 — Verificación: git status

**Guión:** Dentro de un repositorio, corran `git status` y léanme en voz alta qué dice la
terminal. Vamos a volver a este comando todo el curso, así que confirmemos que lo entienden
ahora.

## Diapositiva 9 — Acción: creá tu repositorio en GitHub

**Guión:** Vayan a GitHub y creen un repositorio nuevo: privado, vacío, sin README inicial.
Cuando lo tengan, copien la URL con el botón "Code" → HTTPS.

## Diapositiva 10 — Acción: cloná tu repositorio

**Guión:** En la terminal, paren en la carpeta donde va a vivir su trabajo del curso y corran
`git clone <URL>`. Esa carpeta clonada ES su `curso-ai-powered/` de acá en adelante — ya tiene
`.git` inicializado. Verifiquen con `git status`: debería decir "nothing to commit, working tree
clean".

## Diapositiva 11 — Acción: tu primer commit

**Guión:** Con el repo ya clonado, hagan un cambio chico — por ejemplo una línea en `README.md`.
Corran el flujo completo: `git add .` marca el cambio, `git commit -m "mensaje corto"` lo guarda
en el historial, `git push` lo sube. Este es el flujo mínimo completo del tema: editar, add,
commit, push. Verifiquen en GitHub, en el navegador, que el commit llegó.

## Diapositiva 12 — ¿Qué es ingeniería de prompt?

**Guión:** Ahora sumamos una herramienta que van a usar todo el curso: ingeniería de prompt. Es
la práctica de escribir instrucciones claras y con contexto suficiente para que el asistente dé
el resultado que necesitan, en vez de una respuesta genérica.

## Diapositiva 13 — Las 4 piezas de un buen prompt

**Guión:** Todo buen prompt tiene las mismas 4 piezas, sin importar el tema: tarea (qué querés
que haga), contexto (qué necesita saber para no inventar), restricciones (qué NO tiene que
hacer) y formato (cómo tiene que verse el resultado). Dominan esto y lo aplican en cualquier
área, sepan o no del tema.

## Diapositiva 14 — Ejemplo: prompt vago vs. prompt con contexto

**Guión:** Comparemos. Vago: "dame el contexto del proyecto". Con contexto: "corré la skill
`project-context` en modo `test-plan` sobre lo que ya tengo en `.context/business/`, y contame
qué journeys de negocio identificó". El segundo le dice QUÉ herramienta usar, CÓMO, y SOBRE QUÉ
datos.

## Diapositiva 15 — Consejos para aplicar ingeniería de prompt

**Guión:** Cinco consejos para llevarse hoy. Uno: definan el resultado observable antes de
escribir el prompt — si no pueden describir cómo se ve un output perfecto, no lo van a poder
pedir ni evaluar. Dos: las 4 piezas siempre son las mismas, tarea, contexto, restricciones,
formato. Tres: el "qué NO hacer" pesa tanto como el "qué hacer" — frenar errores típicos evita
que el modelo invente. Cuatro: usen al modelo para investigar antes de pedirle que ejecute — no
necesitan dominar el dominio, necesitan saber preguntar qué les falta saber. Cinco: iteren y
guarden lo que funciona — un prompt se prueba, se ajusta, y se convierte en plantilla reusable.

## Diapositiva 16 — ¿Qué es una skill?

**Guión:** Ese último consejo, guardar lo que funciona como plantilla reusable, es exactamente lo
que resuelve una skill. Una skill es un paquete de instrucciones que le enseña al asistente una
tarea específica de forma reproducible, en vez de explicarle todo de cero cada vez que la
necesitan.

## Diapositiva 17 — Prompt vs. skill: la diferencia

**Guión:** ¿En qué se diferencia de un prompt? Un prompt es de un solo uso: lo escriben, lo
mandan, se pierde al cerrar la conversación. Una skill toma ese mismo trabajo de ingeniería de
prompt ya afinado y lo empaqueta para reusar: se guarda en disco, persiste entre conversaciones,
y el asistente decide solo cuándo aplicarla.

## Diapositiva 18 — Acción: miren dos skills reales

**Guión:** Abran `.agents/skills/project-context/` y `.agents/skills/project-discovery/`.
`project-context` genera los mapas de negocio (datos, funcionalidades, API) y el plan de
pruebas, se regenera cada sprint. `project-discovery` genera el glosario de dominio y el mapa de
personas — el vocabulario base que las demás skills asumen resuelto. Las dos tienen la misma
forma: `SKILL.md` + `references/`. Hoy vemos la forma y un resumen de qué hace cada una — para
profundizar un modo hay que leer su `references/` completo por su cuenta. Cómo se usan de verdad
lo vemos la próxima clase.

## Diapositiva 19 — Cómo se arma una buena skill

**Guión:** Cinco cosas que hacen que una skill funcione. Uno: es un paquete reutilizable, no una
instrucción de una sola vez — se guarda como carpeta con un `SKILL.md` y persiste entre
conversaciones. Dos: la descripción es la parte más crítica — es lo único que el asistente mira
para decidir si aplica; si es débil, la skill nunca se activa. Tres: se carga en capas — nombre y
descripción primero, cuerpo completo después, recursos extra solo si hacen falta. Cuatro:
instrucciones claras, en modo imperativo, con ejemplos — un buen ejemplo de input-output vale más
que párrafos de reglas. Cinco: se crea igual que un buen prompt, se prueba y se ajusta — la
diferencia es que una vez afinada queda guardada como activo permanente.

## Diapositiva 20 — Cierre y checklist

**Guión:** Repasemos: navegan carpetas con `cd`/`ls`/`cd ..`, explican Git/GitHub/repositorio con
sus propias palabras, tienen `curso-ai-powered/` clonado desde su propio repo, corrieron el
flujo mínimo completo (`fetch`/`status`/`pull` para sincronizar, `add`/`commit`/`push` para subir
sus propios cambios), aplicaron las 4 piezas para escribir un prompt mejorado, distinguieron una
skill de un prompt suelto, y saben qué hace `project-context` y qué hace `project-discovery` sin
haberlas usado todavía. La próxima clase las usamos de verdad.
