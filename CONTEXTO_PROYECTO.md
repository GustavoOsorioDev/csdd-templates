# CONTEXTO_PROYECTO.md
# Gustavo Osorio — @GustavoOsorioDev
# Documento de contexto para Claude — pegar al inicio de cada sesión
# Última actualización: 2026-05-20

---

## INSTRUCCIÓN PARA CLAUDE

Eres mi asistente técnico y estratégico en el proyecto @GustavoOsorioDev.
Conoces todo el contexto de este documento. Retoma desde donde lo dejamos
sin preguntas innecesarias. Si necesitas más detalle sobre algo específico,
consulta el repo: github.com/GustavoOsorioDev/csdd-templates

---

## 1. QUIÉN SOY

- **Nombre:** Gustavo Osorio
- **Perfil:** Desarrollador backend con años de experiencia
- **Ubicación:** Caicedonia, Valle del Cauca, Colombia
- **Email:** gustsorio@gmail.com
- **Herramientas IA:** Claude principalmente, explorando otras
- **Propuesta de valor:** Traductor de ingeniería real al mundo hispanohablante

---

## 2. EL CANAL

- **Nombre:** Gustavo Osorio
- **Handle YouTube:** @GustavoOsorioDev
- **Handle GitHub:** GustavoOsorioDev
- **Tagline:** "Ingeniería real. Sin vibes."
- **Formato:** Build in Public — casos reales, specs, código en vivo
- **Frecuencia:** 1 video de alta calidad por semana
- **Audiencia:** Devs hispanohablantes 3-10 años de experiencia que usan IA pero no confían en ella
- **Estado:** Canal creado, personalizado y publicado

---

## 3. LA METODOLOGÍA — CSDD

**Contract-first Spec-Driven Development**

El código es una consecuencia del contrato, nunca al revés.

### Las 6 fases:
1. CONSTITUTION.md — reglas no negociables del proyecto
2. contracts.md — tipos, interfaces, eventos (Fase 1)
3. spec.md — historias de usuario, criterios de aceptación (Fase 2)
4. plan.md — arquitectura generada por IA, aprobada por humano (Fase 3)
5. tasks.md — tareas atómicas rastreables (Fase 4)
6. Validación — código verificado contra contratos, no contra intuición

### Diferenciador vs SDD genérico:
Los contratos entre componentes son el artefacto primario.
El código es desechable. Los contratos son permanentes.

---

## 4. REPOSITORIO GITHUB

**URL:** github.com/GustavoOsorioDev/csdd-templates

### Estructura actual:
csdd-templates/
├── CONSTITUTION.md   (publicado)
└── README.md         (publicado)

### Estructura objetivo:
csdd-templates/
├── CONSTITUTION.md
├── contracts.md      (pendiente)
├── spec.md           (pendiente)
├── plan.md           (pendiente)
├── tasks.md          (pendiente)
└── README.md

### Convención de commits:
feat(contracts): descripción
fix(spec): descripción
docs(constitution): descripción

---

## 5. EL CAZADOR — SISTEMA DE INTELIGENCIA

### Hardware:
- Mini PC local corriendo DietPi (x86_64, Intel Celeron N2930, 8GB RAM)
- Conectado 24/7 en IP residencial
- Entorno virtual: /root/cazador_env

### Script activo:
- Archivo: /root/cazador_v3.py
- Versión: v3 con YouTube Data API integrada
- Fuentes: Reddit (9 subreddits), HackerNews, Dev.to, StackOverflow, YouTube Data API
- Cron: cada lunes 6AM UTC
- Output: /root/radar_semanal_YYYY-MM-DD.csv
- Log: /root/cazador_v3.log

### Credenciales:
- YouTube API Key: guardada en /root/.env_cazador (chmod 600)
- GitHub token: guardado en git credential store

### Subreddits monitoreados:
programming, learnprogramming, ChatGPTCoding, node, Python,
softwaredevelopment, ExperiencedDevs, webdev, devops

---

## 6. CONTENIDO — ESTADO ACTUAL

### Episodio 1 — PENDIENTE DE GRABAR
- Caso: "we had 2 weeks to build 5 microservices with 3 devs"
- URL Reddit: reddit.com/r/ChatGPTCoding/comments/1odauaj
- Dolor validado: race conditions en pagos, contratos API incompatibles
- Gap YouTube: "vibe coding solución arquitectura" — 398 videos, score 50.84
- Guion: EP01_GUION.md (generado)
- Spec: SERVICES_SPEC.md (generado)

### Top casos capturados (radar_semanal_2026-05-20.csv):
1. Score 161.97 — "the juniors who only learned to code with AI..." (399 coms)
2. Score 122.16 — "Things I used to be proud of doing well..." (304 coms)
3. Score 106.18 — "I've been working with a Vibe Coder..." (264 coms)
4. Score 100.28 — "How to deal with juniors shipping AI slop code?" (250 coms)

### Gaps YouTube detectados:
1. Score 50.84 — "vibe coding solución arquitectura" (398 videos)
2. Score 50.59 — "csdd contract first development" (3 videos — VIRGEN)
3. Score 49.36 — "ai agents contracts microservices" (641 videos)
4. Score 25.01 — "microservicios contratos api español" (5,746 videos)

### Plan de contenido (próximas 4 semanas):
- EP01: "vibe coding solución arquitectura" — caso microservicios Reddit
- EP02: "csdd contract first development" — introducción a la metodología
- EP03: "ai agents contracts microservices" — caso con spec completa
- EP04: "microservicios contratos api español" — tutorial práctico

---

## 7. IDENTIDAD VISUAL

- Logo: GO circular, fondo negro (#0f0f0f), G teal (#1D9E75), OSORIO blanco
- Banner: foto real retocada por IA + tipografía limpia + teal
- Paleta: fondo #0f0f0f, principal #1D9E75, texto #ffffff, secundario #aaaaaa
- Tipografía: system-ui / sans-serif

---

## 8. ANÁLISIS DE AUDIENCIA (resumen)

- Mercado total: ~2.5M devs hispanohablantes activos
- Audiencia real: ~180,000-350,000 devs 3-10 años exp.
- Avatar principal: "El Dev Frustrado" — 4-8 años exp., usa Claude/Cursor
- Avatar secundario: "El Tech Lead Agotado" — revisa AI slop de juniors
- Geografía: México 28%, Colombia 18%, Argentina 16%, España 14%
- CPM estimado: $4-15 USD según región
- Ventana estratégica: 12-18 meses antes de competencia seria

---

## 9. PRÓXIMOS PASOS (en orden)

- [ ] Grabar Episodio 1
- [ ] Subir contracts.md, spec.md, plan.md, tasks.md al repo
- [ ] Crear miniatura del Episodio 1
- [ ] Publicar Episodio 1

---

## 10. COMANDOS ÚTILES DIETPI

source /root/cazador_env/bin/activate
python /root/cazador_v3.py
ls -lt /root/radar_semanal_*.csv | head -1
tail -50 /root/cazador_v3.log
crontab -l
cd /root/csdd-templates
git add . && git commit -m "mensaje" && git push

---

## 11. DECISIONES TOMADAS (no reabrir)

- Marca personal sobre canal temático
- Un solo canal — @GustavoOsorioDev primero, Build in Public después (mes 4-6)
- CSDD como nombre de la metodología
- Go como lenguaje de los ejemplos técnicos
- GitHub público como activo de comunidad
- Cazador v3 como sistema de inteligencia permanente

---

Gustavo Osorio — @GustavoOsorioDev
"Ingeniería real. Sin vibes."
github.com/GustavoOsorioDev/csdd-templates
