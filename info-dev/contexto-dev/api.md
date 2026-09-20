# Generador del Mapa de Negocio de la API

> **Orden de generación: 5º de 5.** Generá este documento al final, después de `data.md`,
> `glossary.md`, `personas.md` y `features.md`. Usa los cuatro para elegir mejor los recorridos de
> negocio críticos y nombrar roles de forma consistente. Ver `README.md` de esta carpeta para el
> orden completo.

Genera o actualiza `.context/business/business-api-map.md` — un mapa centrado en el negocio de
cómo la API del sistema potencia los recorridos del usuario.

**Objetivo**: analizá el producto completo (o indicame una ruta/módulo puntual si querés acotar el
análisis).

---

## Qué produce esto

Un único documento que explica **cómo opera el negocio a través de la API**, cubriendo:
- El modelo de permisos y auth (niveles, flujo de tokens, dónde se hace cumplir)
- Recorridos de negocio críticos trazados como cadenas de llamadas a la API de punta a punta
- La arquitectura detrás de la API (servicios, persistencia, límites)
- Integraciones externas en el borde de la API (pagos, auth, email, webhooks)
- Referencias cruzadas a entidades de `business-data-map.md` y funcionalidades de
  `business-feature-map.md`

Este es el complemento **narrativo** de `business-data-map.md` (centrado en datos) y
`business-feature-map.md` (centrado en capacidades).

**No es un catálogo de endpoints.** Ver §Qué NO está en este documento.

---

## Fuentes (usá TODAS las disponibles)

Agotá cada fuente. Preferí los archivos de contexto existentes antes que re-derivar desde el código.

| Fuente | Qué extraer | Herramienta |
|--------|-----------------|------|
| Spec OpenAPI | Inventario de endpoints, tags de auth, formas de request/response | OpenAPI MCP, o el archivo/URL del spec del proyecto |
| `.context/business/business-data-map.md` | Entidades, flujos, máquinas de estado — los recorridos deben alinearse a esto | Leer directo |
| `.context/business/business-feature-map.md` | Funcionalidades, CRUD, integraciones — los endpoints pertenecen a funcionalidades | Leer directo (si falta, tratalo como Discovery Gap) |
| Auth middleware | Dónde se validan los tokens, cómo mapean los roles, límites público-vs-protegido | Leé el repositorio de backend — auth/, middleware/, guards/, decorators |
| Controllers / rutas | Forma de los handlers y efectos colaterales detrás de cada endpoint | Mismo repositorio de backend — controllers, servicios |
| Dependencias del package | SDKs externos en el borde de la API (Stripe, Auth0, Resend, S3, etc.) | Leé `package.json`, `requirements.txt`, `Gemfile` |
| Env / config (solo ejemplos) | Config del proveedor de auth, endpoints de integración, URLs de webhook | Leé `.env.example`, archivos de config — NUNCA leas ni vuelques secretos reales |
| Docs existentes | Notas de API escritas a mano, guías de onboarding | PRD, SRS, docs internas del proyecto |

**Regla de oro**: este es un documento *narrativo*. Si el OpenAPI ya expresa un hecho como schema,
enlazalo — NO lo repitas en prosa.

---

## Detección de modo

```
¿Existe .context/business/business-api-map.md?
  → NO:  generalo desde cero
  → SÍ:  generá una nueva versión, mostrame el resumen del diff, y
         esperá mi confirmación antes de sobreescribir. NUNCA
         sobreescribas automáticamente.
```

---

## Gates de dependencia

Los 3 gates de archivos de contexto son **soft** — este comando produce valor incluso en repos
poco documentados; los inputs faltantes se vuelven Discovery Gaps, no un freno total.

- **Falta `business-data-map.md`** → avisame ("los recorridos van a ser más débiles sin contexto
  de entidades"), seguí adelante, registrá la limitación en §Discovery Gaps.
- **Falta `domain-glossary.md` o `business-personas-map.md`** → avisame ("el modelo de permisos va
  a nombrar roles ad-hoc en vez de citar los ya documentados"), seguí adelante, registrá la
  limitación en §Discovery Gaps.
- **Falta `business-feature-map.md`** → avisame ("la selección de recorridos va a depender solo
  del escaneo de código"), seguí adelante, registrá la limitación en §Discovery Gaps.
- **Sin spec OpenAPI Y sin backend escaneable por rutas** → freno total. Avisame que necesitás que
  exponga un spec o alguna forma de descubrir las rutas; no se puede producir un mapa de API sin
  ninguno de los dos.

---

## Fases de descubrimiento

### Fase 1 — Modelo de permisos y auth

Identificá los niveles y cómo un caller llega a cada uno.
- ¿Qué esquemas de autenticación existen? (JWT, session cookie, API key, OAuth — tratá cada uno
  como su propio nivel, no como un "Protegido" genérico)
- ¿Cómo obtiene un usuario un token? (endpoint de login, flujo SSO, receta de refresh)
- ¿Qué roles/scopes/claims habilitan niveles más altos? (admin, owner, scoped por tenant)
- ¿Dónde vive la validación? (middleware, guard, decorator, edge function)

**Resultado**: una taxonomía (Público / Autenticado / Basado en rol / Scoped por owner) anclada a
rutas de código concretas. NO listes endpoints por nivel acá — eso es trabajo del feature-map. Si
`business-personas-map.md` ya existe, citá sus roles por nombre en vez de re-derivarlos desde
cero.

### Fase 2 — Recorridos de negocio críticos

Elegí **3 a 7 recorridos** que más le importan al negocio. Priorizá por:
- Impacto en ingresos (checkout, facturación, suscripción)
- Seguridad (auth, reset de password, cambios de permisos, impersonation)
- Valor central para el usuario (el flujo principal del producto)
- Alto radio de impacto ante falla (transferencias de fondos, exportación de datos, operaciones
  masivas)

Para cada recorrido seleccionado, trazá la cadena: `Cliente → Auth → Handler → Servicios → DB /
Externo → Respuesta`. Referenciá los FEAT-ID del feature-map y las entidades del data-map. NO
inventes recorridos — cruzá referencias con el contexto existente primero.

### Fase 3 — Arquitectura detrás de la API

Una capa de profundidad, no exhaustiva:
- ¿Qué servicios están detrás de la API? (módulo monolítico, microservicio, background worker)
- ¿Qué persistencia toca? (DB primaria, cache, cola, almacenamiento de objetos)
- ¿Qué forma de deployment tiene? (función serverless, contenedor, edge worker, lambda)

Propósito: orientar a un QA nuevo sobre "qué se rompe si la API se cuelga acá".

### Fase 4 — Integraciones externas en el borde de la API

Para cada servicio de terceros alcanzado desde la API:
- ¿Qué dispara la llamada? (endpoint, webhook entrante, webhook saliente, background job)
- ¿Cuál es el modo de falla visible para el usuario? (timeout, falla silenciosa, retry encolado,
  error duro)
- ¿Qué recorridos críticos dependen de él?

Tomá info de feature-map §Integraciones con terceros si está disponible; enriquecé con la columna
de modo de falla que ese documento omite.

### Fase 5 — Referencias cruzadas con data-map y feature-map

Validá coherencia, no dupliques contenido:
- Cada recorrido toca entidades que existen en el data-map — marcá los huérfanos.
- Cada recorrido mapea a funcionalidades del feature-map — marcá las rutas API-only no capturadas
  como funcionalidades.
- Cada integración listada en feature-map que llega al borde de la API aparece acá (y viceversa).

---

## Estructura de la salida

Escribí `.context/business/business-api-map.md` con estas 7 secciones:

### 1. Resumen ejecutivo

2-3 párrafos respondiendo *¿qué le permite hacer esta API al negocio?* Encuadralo desde la
perspectiva del usuario — "los compradores autenticados completan una compra en cuatro llamadas",
no "la API expone 47 endpoints". Evitá conteos y listados de endpoints.

### 2. Modelo de permisos y auth

- Tabla de niveles: `Nivel | A quién aplica | Cómo se obtiene | Dónde se hace cumplir (ruta de código)`.
- Diagrama ASCII del flujo de tokens para el esquema de auth principal (login → token → llamada
  siguiente → refresh).
- Si coexisten varios esquemas (JWT + API key + session cookie), incluí un diagrama por esquema.

Sin listados por endpoint acá.

### 3. Recorridos de negocio críticos

Una subsección por recorrido. Cada una con:
- Nombre + propósito de negocio en una frase.
- Diagrama de secuencia ASCII: `Cliente → Middleware → Handler → DB / Externo → Respuesta`.
- Narrativa numerada (1..N) con el *por qué* de cada paso.
- **Endpoints involucrados**: lista de punteros `METHOD /path` (no specs completos — enlazá al OpenAPI).
- **Entidades tocadas**: punteros a entidades del data-map.
- **Feature IDs**: punteros a FEAT-NNN del feature-map (omitir si no hay).

Tope de 7 recorridos por defecto. Si el sistema genuinamente tiene más flujos críticos, documentá
la decisión del tope en §Discovery Gaps en vez de expandir en silencio.

### 4. Arquitectura detrás de la API

- Un diagrama ASCII en capas: `Cliente → API Gateway / Edge → Handlers → Servicios → Persistencia / Externo`.
- Tabla: `Componente | Rol | Persistencia/Integraciones tocadas | Por qué le importa a QA`.

Un solo diagrama para todo el sistema, no uno por recorrido.

### 5. Integraciones externas

```markdown
| Servicio | Disparador | Dirección | Modo de falla (visible al usuario) | Recorridos afectados |
|---------|---------|-----------|-----------------------------|-------------------|
| Stripe  | POST /checkout | Saliente sync | 5xx → orden se queda en `pending` | Checkout |
| Stripe  | webhook /stripe/events | Entrante async | evento perdido → la orden nunca se finaliza | Checkout |
```

### 6. Referencias cruzadas

- Entidades del data-map que esta API expone → punteros a anchors de `.context/business/business-data-map.md`.
- Funcionalidades del feature-map que esta API respalda → punteros a anchors de
  `.context/business/business-feature-map.md`.
- Ubicación del spec OpenAPI (ruta de archivo o URL) para los specs completos de endpoints.

Propósito: que quede obvio dónde vive cada tipo de información de API, para que nada se
re-documente acá.

### 7. Discovery gaps

OBLIGATORIO. Listá todo lo que no pudiste verificar:
- Esquemas de auth observados en código pero ausentes del middleware (o viceversa).
- Recorridos que no se pudieron trazar de punta a punta (ramas muertas, evidencia faltante).
- Integraciones mencionadas en env pero sin llamadas de código (¿planeadas, muertas, o no
  documentadas?).
- Shards de monorepo no inspeccionados.
- Webhooks configurados en dashboards externos pero no descubribles desde el código.

Agregá una línea `> Última verificación contra OpenAPI el YYYY-MM-DD` (fecha de hoy) al tope de la
salida.

---

## Después de generar

- Si tu proyecto tiene un archivo de memoria para la IA (`AGENTS.md`, `CLAUDE.md`, `README.md` de
  contexto, etc.), agregá ahí una referencia al archivo generado, si todavía no está.
- Si `business-data-map.md` o `business-feature-map.md` faltaban durante la generación, anotá la
  limitación en el resumen que me reportás.
- Si el archivo ya existía: mostrame el resumen del diff y esperá mi confirmación explícita antes
  de sobreescribir.
- Reportá: niveles de auth documentados, recorridos trazados, servicios detrás de la API,
  integraciones mapeadas, discovery gaps.

---

## Qué NO está en este documento

Este comando hace una sola cosa: narrar la **historia de la API a nivel de negocio**. Todo lo de
abajo queda fuera de alcance:

| Fuera de alcance | Dónde va |
|--------------|-------|
| Catálogo exhaustivo de endpoints (cada ruta con request/response) | Spec OpenAPI |
| Tipos TypeScript para las formas de request/response | Generador de tipos del proyecto, si tiene uno |
| Recetas de cURL / Postman / DevTools | Colección de API del proyecto |
| Diseño de casos de prueba por endpoint (matriz happy/error) | Trabajo del equipo de QA, no de este documento |
| Matriz CRUD por entidad | `business-feature-map.md` |
| Inventario de componentes de UI | `business-feature-map.md` |
| Schemas de entidades, máquinas de estado, reglas de negocio | `business-data-map.md` |
| Roadmap de pruebas priorizado por riesgo ("qué testear y por qué") | Trabajo del equipo de QA, no de este documento |

Si te piden algo de la lista de arriba dentro de `business-api-map.md`, no lo agregues acá — este
documento se mantiene corto, narrativo, y centrado en el negocio.

---

Este era el último de los 5 documentos. Subilos a `.context/business/` en el repo del producto y
entregaselos al equipo de QA según la sección "Cómo entregarlo al QA" de `../guia-dev-contexto.md`.
