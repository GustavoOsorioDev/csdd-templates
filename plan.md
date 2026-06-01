# plan.md
# Gustavo Osorio — CSDD Ecosystem
# Fase 3: Plan de Arquitectura
# Versión: 1.0.0

> El cómo. Solo después del qué y el por qué.
> La arquitectura sirve a los contratos, no al revés.
> Si una decisión aquí viola contracts.md, este archivo está mal.

---

## Instrucciones para el agente

Lee contracts.md y spec.md antes de leer este archivo.
Este archivo define la estructura física del sistema: carpetas, capas, dependencias.
No agregues capas, librerías ni abstracciones no listadas aquí.
Cada decisión arquitectónica tiene una razón. Si no la ves, pregunta antes de asumir.

---

## Stack tecnológico

<!--
Lista exacta de tecnologías. Sin alternativas, sin "o también podría ser".
El agente usa exactamente esto. Si no está aquí, no se usa.
-->

| Capa | Tecnología | Versión | Razón |
|------|-----------|---------|-------|
| [capa] | [tecnología] | [versión] | [por qué esta y no otra] |

---

<!-- EJEMPLO REAL -->
| Capa | Tecnología | Versión | Razón |
|------|-----------|---------|-------|
| Runtime | Node.js | 20 LTS | estabilidad, soporte largo plazo |
| Framework | Fastify | 4.x | performance, schema validation nativo |
| Lenguaje | TypeScript | 5.x | tipos = contratos ejecutables |
| Base de datos | PostgreSQL | 16 | relacional, ACID, sin sorpresas |
| ORM | Drizzle | latest | type-safe, sin magic, SQL legible |
| Autenticación | JWT | — | stateless, simple para v1 |
| Tests | Vitest | latest | compatible con TS sin config extra |
<!-- FIN EJEMPLO -->

---

## Estructura de carpetas

<!--
Define la estructura exacta. El agente no crea carpetas fuera de este mapa.
Incluye para qué sirve cada carpeta en una línea.
-->

```
[nombre-proyecto]/
├── [carpeta]/          # [para qué sirve]
│   ├── [subcarpeta]/   # [para qué sirve]
│   └── [archivo]       # [para qué sirve]
└── [archivo]           # [para qué sirve]
```

---

<!-- EJEMPLO REAL -->
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
<!-- FIN EJEMPLO -->

---

## Arquitectura de capas

<!--
Define el flujo de una request de inicio a fin.
Qué capa llama a qué otra. Qué capas NO deben conocerse entre sí.
-->

```
[Entrada] → [Capa A] → [Capa B] → [Capa C] → [Salida]
```

**Reglas de dependencia:**
- [Capa A] puede llamar a [Capa B], nunca al revés
- [Capa X] no conoce [Capa Y]

---

<!-- EJEMPLO REAL -->
```
HTTP Request → Route Handler → Service → DB (Drizzle) → PostgreSQL
                    ↓
              Middleware (auth, validación)
```

**Reglas de dependencia:**
- `routes/` llama a `services/` — nunca a `db/` directamente
- `services/` llama a `db/` — nunca a `routes/`
- `middleware/` no conoce `services/`
- Los tipos de `contracts/` son importados por todas las capas — son la única dependencia cruzada permitida
<!-- FIN EJEMPLO -->

---

## Base de datos

<!--
Define las tablas derivadas de las entidades en contracts.md.
Si contracts.md cambia, este esquema cambia. No al revés.
Incluye índices que sabes que vas a necesitar desde el inicio.
-->

### Tabla: [nombre_tabla]

| Columna | Tipo DB | Constraint | Deriva de |
|---------|---------|-----------|-----------|
| [col] | [tipo] | [constraint] | [campo en contracts.md] |

---

<!-- EJEMPLO REAL -->
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
- `tasks(status)` — queries de filtro frecuentes
- `tasks(assignee_id)` — queries de filtro frecuentes

### Tabla: users

| Columna | Tipo DB | Constraint | Deriva de |
|---------|---------|-----------|-----------|
| id | uuid | PRIMARY KEY | User.id |
| email | varchar(255) | NOT NULL, UNIQUE | User.email |
| name | varchar(100) | NOT NULL | User.name |
| role | varchar(20) | NOT NULL, CHECK | User.role |
| created_at | timestamptz | NOT NULL, DEFAULT now() | User.createdAt |
<!-- FIN EJEMPLO -->

---

## Variables de entorno

<!--
Lista todas las variables que el sistema necesita para correr.
Sin defaults en producción para secretos. El agente genera el .env.example.
-->

| Variable | Requerida | Ejemplo | Para qué |
|----------|-----------|---------|----------|
| [VAR] | sí/no | [ejemplo] | [para qué se usa] |

---

<!-- EJEMPLO REAL -->
| Variable | Requerida | Ejemplo | Para qué |
|----------|-----------|---------|----------|
| DATABASE_URL | sí | postgresql://... | conexión a PostgreSQL |
| JWT_SECRET | sí | — | firma de tokens |
| PORT | no | 3000 | puerto del servidor |
| NODE_ENV | no | development | comportamiento de logs y errores |
<!-- FIN EJEMPLO -->

---

## Decisiones técnicas registradas

<!--
Cada decisión importante que tomaste y por qué.
Esto evita que el agente (o tú en 3 meses) rehaga la misma discusión.
Formato: qué se decidió, por qué, qué se descartó.
-->

### [Fecha] — [Título de la decisión]

**Decisión:** [qué se eligió]
**Razón:** [por qué]
**Descartado:** [qué alternativa se consideró y por qué no]

---

<!-- EJEMPLO REAL -->
### 2025-06-01 — ORM: Drizzle sobre Prisma

**Decisión:** usar Drizzle ORM
**Razón:** genera SQL predecible, no oculta la base de datos, el esquema vive en TypeScript puro sin DSL propio
**Descartado:** Prisma — demasiada magia, esquema en lenguaje propio que aleja del contrato TypeScript

### 2025-06-01 — Sin capa de repositorio en v1

**Decisión:** services acceden a Drizzle directamente, sin abstracción de repositorio
**Razón:** complejidad innecesaria para v1 con un solo storage; se agrega si hay necesidad real
**Descartado:** patrón Repository — over-engineering para este scope
<!-- FIN EJEMPLO -->

---

Gustavo Osorio — @GustavoOsorioDev
Ingeniería real. Sin vibes.
