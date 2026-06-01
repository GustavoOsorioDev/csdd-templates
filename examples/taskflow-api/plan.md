# plan.md
# TaskFlow API — CSDD Example
# Fase 3: Plan de Arquitectura
# Versión: 1.0.0

---

## Stack tecnológico

| Capa | Tecnología | Versión | Razón |
|------|-----------|---------|-------|
| Runtime | Node.js | 20 LTS | estabilidad, soporte largo plazo |
| Framework | Fastify | 4.x | performance, schema validation nativo |
| Lenguaje | TypeScript | 5.x | tipos = contratos ejecutables |
| Base de datos | PostgreSQL | 16 | relacional, ACID, sin sorpresas |
| ORM | Drizzle | latest | type-safe, sin magic, SQL legible |
| Autenticación | JWT | — | stateless, simple para v1 |
| Tests | Vitest | latest | compatible con TS sin config extra |

---

## Estructura de carpetas

```
taskflow-api/
├── src/
│   ├── contracts/      # tipos TypeScript — espejo de contracts.md
│   ├── routes/         # handlers HTTP — uno por recurso
│   ├── services/       # lógica de negocio — sin acceso directo a DB
│   ├── db/
│   │   ├── schema.ts   # esquema Drizzle — deriva de contracts.md
│   │   └── client.ts   # instancia única de la conexión
│   ├── middleware/      # auth, validación, error handling
│   └── app.ts          # configuración de Fastify
├── tests/
│   ├── unit/           # tests de servicios — sin DB real
│   └── integration/    # tests de rutas — con DB en memoria o test
├── contracts.md        # fuente de verdad — no el código
├── spec.md
├── plan.md
├── tasks.md
└── CONSTITUTION.md
```

---

## Arquitectura de capas

```
HTTP Request → Route Handler → Service → DB (Drizzle) → PostgreSQL
                    ↓
              Middleware (auth, validación)
```

**Reglas de dependencia:**
- `routes/` llama a `services/` — nunca a `db/` directamente.
- `services/` llama a `db/` — nunca a `routes/` directamente.
- `middleware/` no conoce la existencia de los `services/`.
- Los tipos de `src/contracts/` son el único punto de contacto global.

---

## Base de datos

### Tabla: tasks

| Columna | Tipo DB | Constraint | Deriva de |
|---------|---------|-----------|-----------|
| id | uuid | PRIMARY KEY | Task.id |
| title | varchar(200) | NOT NULL | Task.title |
| description | text | NULL | Task.description |
| status | varchar(20) | NOT NULL, CHECK | Task.status |
| priority | varchar(20) | NOT NULL, CHECK | Task.priority |
| assignee_id | uuid | NULL, FK → users.id | Task.assigneeId |
| due_date | timestamptz | NULL | Task.dueDate |
| created_at | timestamptz | NOT NULL, DEFAULT now() | Task.createdAt |
| updated_at | timestamptz | NOT NULL, DEFAULT now() | Task.updatedAt |

**Índices:**
- `tasks(status)` — consultas de filtrado frecuentes.
- `tasks(assignee_id)` — consultas de filtrado frecuentes.

### Tabla: users

| Columna | Tipo DB | Constraint | Deriva de |
|---------|---------|-----------|-----------|
| id | uuid | PRIMARY KEY | User.id |
| email | varchar(255) | NOT NULL, UNIQUE | User.email |
| name | varchar(100) | NOT NULL | User.name |
| role | varchar(20) | NOT NULL, CHECK | User.role |
| created_at | timestamptz | NOT NULL, DEFAULT now() | User.createdAt |

---

## Variables de entorno

| Variable | Requerida | Ejemplo | Para qué |
|----------|-----------|---------|----------|
| DATABASE_URL | sí | postgresql://... | conexión a PostgreSQL |
| JWT_SECRET | sí | — | firma de tokens |
| PORT | no | 3000 | puerto del servidor |
| NODE_ENV | no | development | logs y manejo de errores |

---

## Decisiones técnicas registradas

### 2025-06-01 — ORM: Drizzle sobre Prisma
**Decisión:** usar Drizzle ORM.
**Razón:** genera SQL predecible, evita DSLs complejos y se integra perfectamente con TypeScript puro.

### 2025-06-01 — Sin capa de repositorio en v1
**Decisión:** los servicios acceden a Drizzle directamente.
**Razón:** simplicidad para v1; el patrón repository se evaluará solo si el storage cambia.

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
