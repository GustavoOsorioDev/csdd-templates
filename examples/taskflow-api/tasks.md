# tasks.md
# TaskFlow API — CSDD Example
# Fase 4: Tareas atómicas
# Versión: 1.0.0

---

## Estados de tarea

- `pending`   — no iniciada
- `in_progress` — trabajo en curso
- `done`      — terminada y verificada
- `blocked`   — detenida por impedimento

---

## Bloque 0 — Setup del proyecto

### T-001 — Inicializar proyecto TypeScript con Fastify
**Estado:** `pending`
**Implementa:** infraestructura base
**Depende de:** ninguna

**Entregables:**
- `package.json`, `tsconfig.json`, `.env.example`.
- Estructura de carpetas definida en plan.md.
- `src/app.ts` con servidor básico.

### T-002 — Configurar base de datos y esquema Drizzle
**Estado:** `pending`
**Implementa:** persistencia
**Depende de:** T-001

**Entregables:**
- `src/db/schema.ts` y `src/db/client.ts`.
- `src/contracts/index.ts` con tipos TS derivados de contracts.md.

---

## Bloque 1 — US-001: Crear tarea

### T-003 — Servicio: crear tarea
**Estado:** `pending`
**Implementa:** US-001 (lógica)
**Depende de:** T-002

**Entregables:**
- `src/services/task.service.ts` con función `createTask`.
- Validaciones de contracts.md sección 5.

### T-004 — Route: POST /tasks
**Estado:** `pending`
**Implementa:** US-001 (API)
**Depende de:** T-003

**Entregables:**
- `src/routes/tasks.route.ts`.
- Responder 201 y manejar errores 400/401.

---

## Bloque 2 — US-002: Cambiar estado

### T-005 — Servicio: cambiar estado de tarea
**Estado:** `pending`
**Implementa:** US-002 (lógica)
**Depende de:** T-003

**Entregables:**
- Función `updateTaskStatus` con validación de transiciones.
- Emisión de evento `task.status.changed`.

### T-006 — Route: PATCH /tasks/:id
**Estado:** `pending`
**Implementa:** US-002 (API)
**Depende de:** T-005

**Entregables:**
- Handler en `src/routes/tasks.route.ts`.
- Manejo de 404 y 400.

---

## Bloque 3 — US-003: Listar tareas

### T-007 — Servicio y route: GET /tasks
**Estado:** `pending`
**Implementa:** US-003
**Depende de:** T-004

**Entregables:**
- Función `listTasks` con filtros acumulables y paginación.
- Handler en `src/routes/tasks.route.ts`.

---

## Registro de bloqueos

| Tarea | Motivo del bloqueo | Acción requerida |
|-------|-------------------|-----------------|
| — | — | — |

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
