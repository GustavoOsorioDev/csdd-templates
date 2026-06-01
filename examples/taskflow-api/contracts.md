# contracts.md
# TaskFlow API — CSDD Example
# Fase 1: Contratos
# Versión: 1.0.0

> Fuente de verdad para tipos, interfaces y reglas de validación.

---

## 1. Entidades del dominio

### Task

```typescript
interface Task {
  id: string              // UUID v4
  title: string           // máx 200 caracteres
  description?: string    // máx 2000 caracteres
  status: TaskStatus
  priority: Priority
  assigneeId?: string     // referencia a User.id
  dueDate?: Date
  createdAt: Date
  updatedAt: Date
}
```

### User

```typescript
interface User {
  id: string              // UUID v4
  email: string           // único en el sistema
  name: string            // máx 100 caracteres
  role: UserRole
  createdAt: Date
}
```

---

## 2. Enums y tipos literales

```typescript
type TaskStatus = 'todo' | 'in_progress' | 'done' | 'cancelled'

type Priority = 'low' | 'medium' | 'high' | 'critical'

type UserRole = 'admin' | 'member' | 'viewer'
```

---

## 3. Contratos de API

### POST /tasks
Crea una nueva tarea.

**Request body:**
```typescript
{
  title: string
  description?: string
  priority: Priority
  assigneeId?: string
  dueDate?: string        // ISO 8601
}
```

**Response 201:**
```typescript
{ data: Task }
```

**Errores:**
- `400` — title vacío, priority inválida, o assigneeId no existe
- `401` — token de autenticación ausente o expirado

---

### GET /tasks
Lista tareas con filtros y paginación.

**Query params:**
```typescript
{
  status?: TaskStatus
  assigneeId?: string
  page?: number           // default: 1
  limit?: number          // default: 20, máx: 100
}
```

**Response 200:**
```typescript
{
  data: Task[]
  pagination: { page: number; limit: number; total: number }
}
```

---

### PATCH /tasks/:id
Actualiza campos de una tarea.

**Request body:**
```typescript
{
  title?: string
  description?: string
  status?: TaskStatus
  priority?: Priority
  assigneeId?: string
  dueDate?: string
}
```

**Response 200:**
```typescript
{ data: Task }
```

**Errores:**
- `400` — ningún campo enviado, o valores inválidos
- `404` — tarea no encontrada

---

### DELETE /tasks/:id
Elimina una tarea permanentemente.

**Response 204:** sin body

**Errores:**
- `404` — tarea no encontrada

---

## 4. Eventos del sistema

### task.status.changed

**Emitido por:** servicio de tareas
**Cuándo:** PATCH /tasks/:id cambia el campo `status`

```typescript
{
  event: 'task.status.changed'
  payload: {
    taskId: string
    previousStatus: TaskStatus
    newStatus: TaskStatus
    changedBy: string       // User.id
  }
  timestamp: Date
}
```

---

## 5. Reglas de validación

- `Task.title` nunca puede ser cadena vacía ni solo espacios.
- `Task.status` solo puede avanzar en el flujo: `todo → in_progress → done`.
- `Task.status` puede ir a `cancelled` desde cualquier estado excepto `done`.
- Un `User` con `role: 'viewer'` no puede crear ni modificar tareas.
- `dueDate` no puede ser una fecha en el pasado al momento de creación.

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
