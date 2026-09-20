# Apuntes — Tema 00b: Git, GitHub y Skill

> Apuntes tomados automáticamente durante la clase, que se dictó en **3 sesiones**. Están
> compactados en un solo documento y reordenados siguiendo la presentación
> (`../00b-git-github-skill/deck/index.html`). Para el detalle de cada tema, ver
> `../00b-git-github-skill/contenido.md` y `guion-diapositivas.md`.
>
> La transcripción automática cometió algunos errores de nombres, corregidos acá: "Cloud" →
> **Claude**, "MSP" → **MCP**, "Curso Powerite" → **curso-ai-powered**, "Agents.md / Cloud.md" →
> **AGENTS.md / CLAUDE.md**.

## Resumen de la clase

- **Git** es un sistema de control de versiones local. **GitHub** es un servicio en la nube que
  aloja repositorios Git.
- Flujo de trabajo diario: `fetch` → `status` → `pull` → `add` → `commit` → `push`.
- Una **ingeniería de prompt** efectiva tiene 4 piezas: Tarea, Contexto, Restricciones y Formato.
- Una **skill** es un prompt reutilizable y persistente, agrupado por función, que se activa por
  palabras clave.
- Se creó el repositorio del curso en GitHub y se resolvió el problema de PATH que impedía usar
  `git` desde la terminal.

---

## 1. Terminal: comandos básicos

- `cd <carpeta>`: cambiar de directorio.
- `ls`: listar el contenido del directorio.
- `cd ..`: subir un nivel.
- **Warp:** no cierres pestañas sueltas de la terminal. Cerrá la ventana principal de Warp para
  conservar las sesiones abiertas.
- **Errores de terminal:** pegá la captura del mensaje de error en ChatGPT (o en tu asistente de
  IA) para que te ayude a resolverlo.
- **Tarea:** buscar los comandos más comunes de la terminal para estudiarlos.

## 2. ¿Qué es Git?

- Sistema de control de versiones **local**: rastrea los cambios en los archivos y permite volver
  a versiones anteriores.
- Rastrea **archivos, no carpetas vacías**. Para versionar una estructura de carpetas, poné un
  archivo placeholder (por ejemplo `.gitkeep`) en cada una.

**Instalación (Windows):**

1. Ejecutar el instalador oficial de Git (incluye Git Bash, una terminal tipo Linux).
2. Cerrar y volver a abrir la terminal para que los cambios surtan efecto.
3. Verificar con `git --version`.

## 3. ¿Qué es GitHub?

- Servicio en la nube para **alojar repositorios Git** y colaborar con otras personas.
- Se puede invitar a otras personas como **colaboradoras** de un repositorio para que gestionen
  archivos junto con vos.

## 4. ¿Qué es un repositorio?

- Carpeta de proyecto que Git versiona. Tiene adentro una carpeta oculta `.git/` con todo el
  historial.
- `git init`: inicializa un repositorio local y crea `.git/`.
- `git clone <URL>`: descarga una copia completa de un repositorio remoto, con todo su historial.
- **`.gitignore`:** archivo de texto que le dice a Git qué **no** versionar (temporales, logs,
  credenciales locales como `.env`, `node_modules/`).

**Conceptos extra vistos en clase (no tienen diapositiva propia):**

- **Ramas:** copias aisladas del código para trabajar en una funcionalidad sin afectar la rama
  principal.
- **Merge / pull request:** combina los cambios de una rama de vuelta en la principal.
- **Conflicto de merge:** ocurre cuando dos ramas modifican las mismas líneas del mismo archivo.
  Git lo marca y se resuelve a mano.

## 5. Comandos git de trabajo diario

**Inicio del día: traer lo que cambió**

| Comando | Qué hace |
|---|---|
| `git fetch` | Descarga los cambios remotos sin aplicarlos. |
| `git status` | Compara la rama local con la remota y muestra cambios pendientes. |
| `git pull` | Fusiona los cambios remotos en la rama local. |

**Durante el día: subir lo que hiciste**

| Comando | Qué hace |
|---|---|
| `git add .` | Prepara todos los archivos modificados para el commit. |
| `git commit -m "Mensaje"` | Guarda los cambios preparados en el historial local, con un mensaje descriptivo. |
| `git push` | Sube los commits locales a la rama remota. |

## 6. Práctica: repositorio en GitHub y clonado

- Se creó el repositorio del curso en GitHub y se invitó a Carolina como colaboradora.
- **Problema encontrado:** el comando `git` no respondía directamente en la terminal de Carolina
  (había que anteponer `npm`), y eso bloqueaba la clonación.
  - **Causa:** configuración incorrecta del `PATH` del sistema.
  - **Resolución:** se siguió una guía de ChatGPT para corregir el `PATH`, reinstalando Git y
    reabriendo la terminal.
- Una vez resuelto: `git clone <URL del repo>` y verificación con `git status`.
- **Cómo mantenerlo al día:** `git fetch` → `git status` → `git pull` para traer los cambios del
  equipo; `git add .` → `git commit -m "..."` → `git push` para subir los propios.

## 7. Ingeniería de prompt

Un prompt efectivo tiene cuatro componentes:

| Pieza | Qué es |
|---|---|
| **Tarea** | La acción principal que debe realizar la IA. |
| **Contexto** | Toda la información relevante para la tarea. |
| **Restricciones** | Límites de alcance y de comportamiento de la IA. |
| **Formato** | La estructura deseada de la salida. |

Con estas piezas los resultados son consistentes y la IA no se desvía.

## 8. Skills

**Prompt vs. skill**

| | Prompt | Skill |
|---|---|---|
| Duración | Instrucción de un solo uso, se pierde al terminar la conversación | Paquete de instrucciones reutilizable y persistente |
| Alcance | Una conversación | Una tarea específica, siempre disponible |
| Activación | La escribís cada vez | Se activa sola por palabras clave en la conversación |

**Cómo se arma una skill**

- Empieza con un encabezado delimitado por tres guiones (`---`) que lleva la **descripción** y las
  **palabras clave de activación**, seguido de la lógica de la skill.
- **Agrupá** los prompts relacionados en una sola skill, con varias referencias. Ejemplo: una
  skill de "Reportes" con referencias para reporte diario, semanal y mensual.
- **Crearla:** con el plugin `skill-creator` de Claude. Usalo también para comparar la skill
  propuesta con las existentes y evitar duplicados.

**Skill de ejemplo: `project-context`**

- Genera un documento de contexto del proyecto analizando el código y la base de datos.
- Se activa con frases como "generalize", "update canonical maps" o "business data map".
- Mapea automáticamente:
  - **Entidades de negocio:** tablas de la base de datos y sus relaciones.
  - **Flujos de negocio:** recorridos de usuario y lógica de negocio.
  - **Máquinas de estado:** campos enum (por ejemplo `pending`, `approved`, `paid`).
  - **Procesos automáticos:** triggers, tareas cron y webhooks.
- **Requisito:** un servidor **MCP** de base de datos para que la IA pueda consultarla.
- **Gaps:** ambigüedades o inconsistencias que la IA no puede resolver desde el código (una tabla
  que el código no usa, un bloque comentado sin explicación).
  1. La IA marca los gaps en su informe.
  2. La persona aclara el propósito (por ejemplo "es deuda técnica").
  3. La IA registra la aclaración y deja de preguntar por ese gap.
  - Sirve para construir una base de conocimiento precisa y evitar la alucinación.

`project-discovery` es la segunda skill que se miró en clase. El uso operativo de ambas se ve en
un tema posterior.

## 9. Testing sin acceso al código fuente

Contexto de la sesión 2: el equipo de desarrollo no da acceso directo al código.

1. Los desarrolladores generan y comparten archivos de contexto que describen el sistema, y los
   suben al repositorio.
2. Se hace `git pull` para bajarlos.
3. La IA usa esos archivos para identificar todas las funciones del sistema.
4. La IA sugiere un ticket de Jira por cada función; la persona los revisa y confirma.
5. La IA usa esos tickets como contexto para navegar la aplicación y verificar los criterios de
   aceptación.

Consultar un contexto específico en lugar de un documento gigante evita gastar tokens de más.

## 10. Adelanto: gestión con Jira (se profundiza en un tema posterior)

- Plan gratuito de Jira, hasta 10 usuarios.
- **Almacenamiento:** el plan gratuito tiene un límite de 2 GB. Para evitarlo, vinculá la
  evidencia (capturas, videos) desde almacenamiento externo, como Google Drive o Slack.
- **Metodología:** tablero Scrum con sprints, para medir la velocidad del equipo.
- **Cambios en tickets:**
  - Modificación de un requisito: crear una historia de usuario nueva que reemplace la anterior.
  - Error de especificación: crear un ticket de mejora. No se editan tickets completados.

---

## Próximos pasos

**Alumno (Carolina)**

- Corregir el `PATH` para que `git` funcione directo, y clonar el repositorio del curso.
- Buscar los comandos más comunes de la terminal para estudiarlos.
- Usar `skill-creator` para convertir sus prompts existentes en skills, agrupando los
  relacionados en una sola skill con varias referencias.
- Crear una skill nueva para una tarea simple (por ejemplo un reporte semanal).
- Leer la documentación de las skills `project-context` y `project-discovery`.
- Leer la carpeta `context dev` para prepararse para las preguntas de los desarrolladores.

**Instructor (Luis)**

- Subir el contenido del Tema 00b al repositorio del curso.
- Subir los archivos de contexto de ejemplo (`AGENTS.md`, `CLAUDE.md`) y la carpeta `context dev`.
- Configurar un proyecto básico de Jira con tablero Scrum en la próxima sesión.
