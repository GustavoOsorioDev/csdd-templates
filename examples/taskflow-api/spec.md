# spec.md
# TaskFlow API — CSDD Example
# Fase 2: Especificación
# Versión: 1.0.0

---

## Contexto del producto

**TaskFlow** es un sistema de gestión de tareas para equipos pequeños.
Resuelve la pérdida de contexto en proyectos colaborativos al centralizar
el estado, la prioridad y la asignación de cada tarea en un solo lugar.

---

## Historias de usuario

### US-001 — Crear tarea

**Como** miembro del equipo,
**quiero** crear una nueva tarea con título y prioridad,
**para** que el equipo sepa qué hay que hacer y con qué urgencia.

**Criterios de aceptación:**
- [ ] Dado un usuario autenticado, cuando envía título y prioridad válidos, entonces se crea la tarea con status `todo` y se retorna con id generado.
- [ ] Dado una tarea creada, cuando se consulta, entonces `createdAt` refleja el momento exacto de creación.
- [ ] Dado un título con solo espacios, cuando se intenta crear, entonces el sistema retorna error 400.
- [ ] Dado una prioridad fuera del enum definido, cuando se intenta crear, entonces el sistema retorna error 400.

**Casos de error:**
- Si el token de autenticación está ausente o expirado → 401 con mensaje claro.
- Si `assigneeId` no corresponde a un usuario existente → 400 con campo específico del error.

**Notas:**
- El status inicial siempre es `todo`. No es configurable por el usuario en creación.

---

### US-002 — Cambiar estado de una tarea

**Como** miembro del equipo,
**quiero** actualizar el estado de una tarea,
**para** que el equipo vea el progreso real del trabajo.

**Criterios de aceptación:**
- [ ] Dado una tarea en `todo`, cuando se cambia a `in_progress`, entonces el cambio se guarda y `updatedAt` se actualiza.
- [ ] Dado una tarea en `done`, cuando se intenta cambiar a cualquier otro estado, entonces el sistema retorna error 400.
- [ ] Dado una tarea en cualquier estado, cuando se cambia a `cancelled`, entonces el sistema acepta el cambio excepto desde `done`.
- [ ] Dado el cambio de estado, entonces el evento `task.status.changed` es emitido con los valores anterior y nuevo.

**Casos de error:**
- Si la tarea no existe → 404.
- Si la transición de estado es inválida → 400 con el motivo explícito.

**Notas:**
- Las transiciones válidas están definidas en contracts.md sección 5.

---

### US-003 — Listar tareas con filtros

**Como** miembro del equipo,
**quiero** ver todas las tareas filtrando por estado o asignado,
**para** enfocarme en lo que me corresponde o lo que está bloqueado.

**Criterios de aceptación:**
- [ ] Dado ningún filtro, cuando se consulta, entonces retorna todas las tareas paginadas (20 por defecto).
- [ ] Dado filtro por `status`, cuando se consulta, entonces retorna solo tareas con ese estado.
- [ ] Dado filtro por `assigneeId`, cuando se consulta, entonces retorna solo tareas asignadas a ese usuario.
- [ ] Dado `limit` mayor a 100, cuando se consulta, entonces el sistema aplica el máximo de 100.

**Casos de error:**
- Si `status` es un valor fuera del enum → 400.

**Notas:**
- Los filtros son acumulables. `status=todo&assigneeId=xyz` retorna tareas todo asignadas a xyz.

---

## Scope fuera de esta versión

- Notificaciones por email — fuera de alcance v1.
- Adjuntos en tareas — fuera de alcance v1.
- Subtareas — fuera de alcance v1.
- Autenticación con OAuth — v1 usa JWT simple, OAuth es v2.

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
