# tasks.md
# Gustavo Osorio — CSDD Ecosystem
# Fase 4: Tareas atómicas
# Versión: 1.0.0

> Una tarea = un agente = un contrato.
> Si una tarea no cabe en un prompt, es dos tareas.
> El agente no empieza una tarea sin leer su contexto completo.

---

## Instrucciones para el agente

Lee CONSTITUTION.md, contracts.md, spec.md y plan.md antes de este archivo.
Ejecuta las tareas en el orden listado. Las dependencias son reales.
Al terminar una tarea: actualiza su estado a `done` y registra qué archivos tocaste.
Si encuentras ambigüedad: detente y pregunta. No asumas.

---

## Estados de tarea

- `pending`   — no iniciada
- `in_progress` — el agente está trabajando en esto ahora
- `done`      — terminada y verificada por humano
- `blocked`   — no puede continuar, razón documentada abajo

---

## Formato de tarea

<!--
Cada tarea tiene:
- ID único (T-001, T-002...)
- Estado actual
- Historia de usuario que implementa (de spec.md)
- Contexto: qué archivos leer, qué contratos respetar
- Entregables: qué archivos crear o modificar exactamente
- Criterio de hecho: cómo sabe el agente que terminó
- Dependencias: qué tareas deben estar done antes
-->

---

### T-001 — [Título corto y accionable]

**Estado:** `pending`
**Implementa:** [US-XXX de spec.md]
**Depende de:** ninguna / [T-XXX]

**Contexto para el agente:**
- Lee: [archivos relevantes]
- Respeta: [sección específica de contracts.md]
- No toques: [archivos o capas fuera de scope]

**Entregables:**
- Crear `[ruta/archivo]` — [qué debe contener]
- Modificar `[ruta/archivo]` — [qué debe cambiar]

**Criterio de hecho:**
- [ ] [verificación concreta y binaria]
- [ ] [verificación concreta y binaria]
- [ ] Tests pasan: `[comando exacto]`

---

<!-- EJEMPLO REAL — Sistema de gestión de tareas -->

## Bloque 0 — Setup del proyecto

### T-001 — Inicializar proyecto TypeScript con Fastify

**Estado:** `pending`
**Implementa:** infraestructura base (prerequisito de todas las US)
**Depende de:** ninguna

**Contexto para el agente:**
- Lee: plan.md sección "Stack tecnológico" y "Estructura de carpetas"
- Usa exactamente las versiones listadas en plan.md
- No instales dependencias no listadas en plan.md

**Entregables:**
- Crear `package.json` con todas las dependencias de plan.md
- Crear `tsconfig.json` con strict mode activado
- Crear estructura de carpetas exacta de plan.md
- Crear `.env.example` con todas las variables de plan.md
- Crear `src/app.ts` con servidor Fastify básico que responde en `GET /health`

**Criterio de hecho:**
- [ ] `npm install` corre sin errores
- [ ] `npm run dev` levanta el servidor sin errores
- [ ] `GET /health` retorna `{ status: 'ok' }` con código 200
- [ ] No hay ninguna carpeta ni archivo fuera de la estructura de plan.md

---

### T-002 — Configurar base de datos y esquema Drizzle

**Estado:** `pending`
**Implementa:** infraestructura de datos (prerequisito de US-001, US-002, US-003)
**Depende de:** T-001

**Contexto para el agente:**
- Lee: contracts.md sección "Entidades del dominio" y "Enums y tipos literales"
- Lee: plan.md sección "Base de datos"
- El esquema Drizzle debe ser espejo exacto de contracts.md — si hay diferencia, contracts.md gana
- No agregues columnas que no estén en contracts.md

**Entregables:**
- Crear `src/db/schema.ts` con tablas `tasks` y `users` exactas de plan.md
- Crear `src/db/client.ts` con instancia única de conexión Drizzle
- Crear `src/contracts/index.ts` con todos los tipos e interfaces de contracts.md en TypeScript

**Criterio de hecho:**
- [ ] `src/contracts/index.ts` tiene tipos que coinciden 1:1 con contracts.md
- [ ] `src/db/schema.ts` tiene todas las columnas, constraints e índices de plan.md
- [ ] La conexión a DB usa `DATABASE_URL` del `.env`
- [ ] No hay tipos definidos fuera de `src/contracts/`

---

## Bloque 1 — US-001: Crear tarea

### T-003 — Servicio: crear tarea

**Estado:** `pending`
**Implementa:** US-001 (lógica de negocio)
**Depende de:** T-002

**Contexto para el agente:**
- Lee: spec.md US-001 completo — cada criterio de aceptación es un caso a implementar
- Lee: contracts.md sección "Reglas de validación"
- Lee: contracts.md sección "Eventos del sistema"
- El servicio no conoce HTTP — solo recibe datos tipados y retorna datos tipados

**Entregables:**
- Crear `src/services/task.service.ts`
- Función `createTask(input: CreateTaskInput): Promise<Task>`
- El status inicial siempre es `todo` — no configurable
- Emite evento `task.status.changed` NO aplica en creación (no hay cambio de status)
- Valida todas las reglas de contracts.md sección 5 antes de escribir en DB

**Criterio de hecho:**
- [ ] Título vacío o solo espacios lanza error con mensaje descriptivo
- [ ] Priority fuera del enum lanza error con el valor inválido en el mensaje
- [ ] assigneeId inexistente lanza error 400
- [ ] Tarea creada tiene `status: 'todo'` siempre
- [ ] Tests unitarios cubren todos los criterios de aceptación de US-001
- [ ] Tests corren: `npm run test -- task.service`

---

### T-004 — Route: POST /tasks

**Estado:** `pending`
**Implementa:** US-001 (capa HTTP)
**Depende de:** T-003

**Contexto para el agente:**
- Lee: contracts.md sección "POST /tasks" completo
- Lee: plan.md sección "Arquitectura de capas" — routes llaman a services, nunca a DB
- El handler solo traduce HTTP ↔ servicio. Sin lógica de negocio aquí.

**Entregables:**
- Crear `src/routes/tasks.route.ts` con el handler de `POST /tasks`
- Validación de schema en la capa Fastify (no en el servicio)
- Responde 201 con `{ data: Task }` en éxito
- Traduce errores del servicio a los códigos HTTP de contracts.md

**Criterio de hecho:**
- [ ] `POST /tasks` con body válido retorna 201 y la tarea creada
- [ ] `POST /tasks` con title vacío retorna 400
- [ ] `POST /tasks` sin token retorna 401
- [ ] `POST /tasks` con priority inválida retorna 400
- [ ] Tests de integración cubren todos los casos anteriores
- [ ] Tests corren: `npm run test -- tasks.route`

---

## Bloque 2 — US-002: Cambiar estado

### T-005 — Servicio: cambiar estado de tarea

**Estado:** `pending`
**Implementa:** US-002 (lógica de negocio)
**Depende de:** T-003

**Contexto para el agente:**
- Lee: spec.md US-002 completo
- Lee: contracts.md sección "Reglas de validación" — transiciones de estado
- Lee: contracts.md sección "Eventos — task.status.changed"
- Las transiciones válidas son: todo → in_progress → done. cancelled desde cualquiera excepto done.

**Entregables:**
- Agregar `updateTaskStatus(taskId: string, newStatus: TaskStatus, userId: string): Promise<Task>` en `src/services/task.service.ts`
- Valida que la transición es permitida antes de escribir
- Emite evento `task.status.changed` con previousStatus y newStatus

**Criterio de hecho:**
- [ ] `done → in_progress` lanza error con la transición inválida en el mensaje
- [ ] `todo → cancelled` funciona
- [ ] `done → cancelled` lanza error
- [ ] Evento `task.status.changed` se emite en cada cambio exitoso
- [ ] `updatedAt` se actualiza en cada cambio
- [ ] Tests unitarios cubren todas las transiciones válidas e inválidas

---

### T-006 — Route: PATCH /tasks/:id

**Estado:** `pending`
**Implementa:** US-002 (capa HTTP)
**Depende de:** T-005

**Contexto para el agente:**
- Lee: contracts.md sección "PATCH /tasks/:id" completo

**Entregables:**
- Agregar handler `PATCH /tasks/:id` en `src/routes/tasks.route.ts`
- Solo acepta los campos listados en contracts.md — rechaza campos extra

**Criterio de hecho:**
- [ ] Transición inválida retorna 400 con motivo explícito
- [ ] Tarea inexistente retorna 404
- [ ] Campo extra en body retorna 400
- [ ] Cambio exitoso retorna 200 con tarea actualizada

---

## Bloque 3 — US-003: Listar tareas

### T-007 — Servicio y route: GET /tasks

**Estado:** `pending`
**Implementa:** US-003
**Depende de:** T-004

**Contexto para el agente:**
- Lee: spec.md US-003 completo
- Lee: contracts.md sección "GET /tasks" completo
- Los filtros son acumulables. La paginación tiene defaults y máximos definidos.

**Entregables:**
- Agregar `listTasks(filters: TaskFilters): Promise<PaginatedResult<Task>>` en task.service.ts
- Agregar handler `GET /tasks` en tasks.route.ts
- `limit` máximo es 100 — si llega mayor, aplica 100 sin error

**Criterio de hecho:**
- [ ] Sin filtros retorna todas las tareas paginadas con default de 20
- [ ] `status=todo` retorna solo tareas todo
- [ ] `status=todo&assigneeId=xxx` retorna tareas todo asignadas a xxx
- [ ] `limit=200` aplica máximo de 100
- [ ] `status` inválido retorna 400

---

## Registro de bloqueos

<!--
Si una tarea queda bloqueada, documenta aquí el motivo.
No dejes una tarea en `blocked` sin explicación.
-->

| Tarea | Motivo del bloqueo | Acción requerida |
|-------|-------------------|-----------------|
| — | — | — |

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
