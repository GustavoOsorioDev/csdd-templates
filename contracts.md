# contracts.md
# Gustavo Osorio — CSDD Ecosystem
# Fase 1: Contratos
# Versión: 1.0.0

> Este archivo se crea antes que cualquier código.
> Si no está aquí, no existe. Si no está tipado, no se implementa.
> Un agente que no encuentra el contrato no debe asumir — debe detenerse.

---

## Instrucciones para el agente

Lee este archivo completo antes de escribir cualquier línea de código.
Cada tipo, interfaz y evento aquí definido es la única fuente de verdad.
Si el código contradice este archivo, el código está mal.

---

## 1. Entidades del dominio

<!--
Una entidad es un objeto con identidad propia que persiste en el tiempo.
Define todos sus campos, tipos y cuáles son requeridos.
No pongas lógica aquí — solo la forma de los datos.
-->

### [NombreEntidad]

```typescript
interface [NombreEntidad] {
  id: string              // requerido — UUID generado en creación
  [campo]: [tipo]         // requerido
  [campo]?: [tipo]        // opcional
  createdAt: Date         // requerido — generado automáticamente
  updatedAt: Date         // requerido — actualizado en cada mutación
}
```

---

<!-- EJEMPLO REAL — elimina esta sección en tu proyecto -->
## EJEMPLO: Sistema de gestión de tareas

### Task

```typescript
interface Task {
  id: string              // requerido — UUID v4
  title: string           // requerido — máx 200 caracteres
  description?: string    // opcional — máx 2000 caracteres
  status: TaskStatus      // requerido — ver enum abajo
  priority: Priority      // requerido — ver enum abajo
  assigneeId?: string     // opcional — referencia a User.id
  dueDate?: Date          // opcional
  createdAt: Date         // requerido — generado automáticamente
  updatedAt: Date         // requerido — actualizado en cada mutación
}
```

### User

```typescript
interface User {
  id: string              // requerido — UUID v4
  email: string           // requerido — único en el sistema
  name: string            // requerido — máx 100 caracteres
  role: UserRole          // requerido — ver enum abajo
  createdAt: Date         // requerido
}
```
<!-- FIN EJEMPLO -->

---

## 2. Enums y tipos literales

<!--
Define aquí todos los valores posibles para campos con opciones fijas.
Ningún valor fuera de estos es válido. El agente no debe inventar valores.
-->

```typescript
type [NombreEnum] = '[valor1]' | '[valor2]' | '[valor3]'
```

---

<!-- EJEMPLO REAL -->
```typescript
type TaskStatus = 'todo' | 'in_progress' | 'done' | 'cancelled'

type Priority = 'low' | 'medium' | 'high' | 'critical'

type UserRole = 'admin' | 'member' | 'viewer'
```
<!-- FIN EJEMPLO -->

---

## 3. Contratos de API

<!--
Define la firma de cada endpoint: método, ruta, body, respuesta y errores.
El agente implementa exactamente esto. Sin endpoints no documentados aquí.
-->

### [MÉTODO] /[ruta]

**Request:**
```typescript
// body o params según corresponda
```

**Response 200:**
```typescript
// estructura exacta de la respuesta exitosa
```

**Errores:**
- `400` — [cuándo ocurre]
- `401` — [cuándo ocurre]
- `404` — [cuándo ocurre]

---

<!-- EJEMPLO REAL -->
### POST /tasks

**Request body:**
```typescript
{
  title: string           // requerido
  description?: string    // opcional
  priority: Priority      // requerido
  assigneeId?: string     // opcional
  dueDate?: string        // opcional — ISO 8601
}
```

**Response 201:**
```typescript
{ data: Task }
```

**Errores:**
- `400` — title vacío, priority inválida, o assigneeId no existe
- `401` — token ausente o expirado

---

### GET /tasks

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

**Response 204:** sin body

**Errores:**
- `404` — tarea no encontrada
<!-- FIN EJEMPLO -->

---

## 4. Eventos del sistema

<!--
Si tu sistema emite eventos (websockets, queues, webhooks), defínelos aquí.
Cada evento tiene nombre, payload tipado y quién lo emite.
Si no tienes eventos, elimina esta sección.
-->

### [nombre.del.evento]

**Emitido por:** [quién]
**Cuándo:** [condición exacta]

```typescript
{
  event: '[nombre.del.evento]'
  payload: { [campo]: [tipo] }
  timestamp: Date
}
```

---

<!-- EJEMPLO REAL -->
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
<!-- FIN EJEMPLO -->

---

## 5. Reglas de validación

<!--
Restricciones que el agente debe aplicar en toda capa: API, DB, lógica.
No son sugerencias — son invariantes del sistema.
-->

- [Campo] nunca puede ser [valor/condición]
- [Operación] solo es válida si [condición]
- [Entidad] no puede existir sin [campo requerido]

---

<!-- EJEMPLO REAL -->
- `Task.title` nunca puede ser cadena vacía ni solo espacios
- `Task.status` solo puede avanzar: `todo → in_progress → done`
- `Task.status` puede ir a `cancelled` desde cualquier estado excepto `done`
- Un `User` con `role: 'viewer'` no puede crear ni modificar tareas
- `dueDate` no puede ser fecha en el pasado al momento de creación
<!-- FIN EJEMPLO -->

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
