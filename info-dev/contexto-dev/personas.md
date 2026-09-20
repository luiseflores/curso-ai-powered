# Generador del Mapa de Personas y Roles

> **Orden de generación: 3º de 5.** Generá este documento después de `glossary.md` — reusa su
> terminología para nombrar personas y funcionalidades de forma consistente. Ver `README.md` de
> esta carpeta para el orden completo.

Genera o actualiza `.context/business/business-personas-map.md` — el catálogo de roles que el
sistema reconoce, con matriz de permisos y cómo conseguir una cuenta de cada uno para pruebas.

**Objetivo**: analizá el producto completo (o el mismo recorte que usaste en los archivos
anteriores, si acotaste el análisis).

---

## Qué produce esto

Un catálogo de los roles/personas que el sistema reconoce, con matriz de permisos y cómo conseguir
una cuenta de cada uno para pruebas. Vos (el QA) vas a tener acceso a un ambiente de QA vivo — este
documento te dice con qué rol probar qué.

---

## Fuentes (usá TODAS las disponibles)

| Fuente | Qué extraer | Herramienta |
|--------|----------------|------|
| Definiciones de rol en código | Enum/tipo de rol, valores exactos | Leé el backend — busca `role`, `userType`, `permission` |
| Middleware / guards de autorización | Qué rutas/acciones exige cada rol | Leé middleware, guards, decorators de auth |
| Renderizado condicional en frontend | Qué ve cada rol en la UI | Leé componentes con lógica tipo `role === 'admin'` |
| Modelo de usuario en DB | Columna de rol, atributos de perfil | DBHub MCP (o tu cliente de DB) |
| `business-feature-map.md` (si ya existe) | Catálogo de funcionalidades para completar "Acceso a funcionalidades" por persona | Leer directo — si no existe todavía, derivalo del escaneo de rutas/UI |

**Regla de oro**: una persona acá es un ROL que el sistema ya reconoce en código, no un perfil de
usuario inventado ("María, 34 años, marketing"). Documentá "admin", "editor", "cliente" — los
roles reales — no arquetipos de investigación de mercado.

---

## Detección de modo

```
¿Existe .context/business/business-personas-map.md?
  → NO:  generalo desde cero
  → SÍ:  generá una nueva versión, mostrame el resumen del diff, y
         esperá mi confirmación antes de sobreescribir. NUNCA
         sobreescribas automáticamente.
```

---

## Fases de exploración

### Fase 1 — Roles del sistema

Para cada rol encontrado en código/DB:
- Valor exacto del rol en código
- Nivel de acceso (público, autenticado, admin, scoped por owner...)
- Dónde se encontró (archivo)

### Fase 2 — Objetivos por persona

Para cada rol:
- ¿Qué funcionalidades puede usar y para qué las usaría?
- Fundamentá cada objetivo en una funcionalidad real, no en suposición

### Fase 3 — Acceso a funcionalidades

Para cada persona, cruzá contra el catálogo de funcionalidades (si `features.md` ya corrió) o
contra las rutas/acciones descubiertas:
- Acceso Total / Limitado / Ninguno por funcionalidad
- Evidencia (ruta de código o regla de UI)

### Fase 4 — Flujos críticos y edge cases por persona

- ¿Cuál es el recorrido principal de esa persona en el sistema? (resumen de una línea)
- ¿Qué casos borde son específicos de ese rol? (ej: "admin editando su propio permiso", "cliente
  con suscripción vencida intentando pagar")

### Fase 5 — Cómo conseguir una cuenta de cada rol

- ¿Existe ya una cuenta de prueba para este rol en el ambiente de QA?
- Si no, ¿cómo se crea una? (flujo de alta, script de seed, pedido manual al equipo dev)
- NO inventes credenciales ni nombres de variable — solo documentá el procedimiento

---

## Estructura de la salida

Escribí `.context/business/business-personas-map.md` con esta estructura:

1. **Resumen de personas** — tabla `Persona | Rol del sistema | Nivel de acceso | Objetivo
   principal`
2. **Por persona** (2-4 típico, no fuerces una quinta si no existe):
   - Identidad: rol (valor en código) + dónde se encontró + nivel de acceso
   - Objetivos: tabla `Objetivo | Funcionalidad que lo soporta`
   - Acceso a funcionalidades: tabla `Funcionalidad | Acceso | Evidencia`
   - Flujo crítico (resumen ASCII de una línea: `Paso1 → Paso2 → Paso3`)
   - Cómo conseguir una cuenta de este rol para pruebas
   - Edge cases específicos de esta persona
3. **Jerarquía de roles** (si existe una relación de herencia entre roles) — diagrama ASCII
4. **Matriz de permisos** — tabla `Permiso | Rol1 | Rol2 | Rol3 | Rol4` con check/cruz por celda
5. **Discovery gaps** — OBLIGATORIO. Roles o permisos que no pudiste verificar.

Agregá al tope del archivo la línea `> Última verificación: YYYY-MM-DD` (fecha de hoy).

---

## Después de generar

- Si el archivo ya existía: mostrame el resumen del diff y esperá mi confirmación explícita antes
  de sobreescribir.
- Reportá: personas documentadas, funcionalidades cruzadas, discovery gaps.

---

Este era el 3º de 5. Seguí con `features.md` (4º de 5) — ahora puede citar estas personas por
nombre en vez de re-derivar roles sueltos.
