# CONSTITUTION.md
# Gustavo Osorio — CSDD Ecosystem
# Contract-first Spec-Driven Development
# Versión: 1.0.0

> Este archivo es el primer commit de cualquier proyecto bajo CSDD.
> No es opcional. No es sugerencia. Es la constitución.
> El código que viole este documento es código inválido.

---

## 0. Manifiesto

El vibecoding genera código que parece funcionar.
La ingeniería real genera código que funciona.

La diferencia no está en la IA que usas.
Está en si tienes un contrato antes de usarla.

**CSDD es la metodología que pone el contrato primero.**
El código es una consecuencia del contrato, nunca al revés.

---

## 1. Principios no negociables

### 1.1 Contract First
Ningún agente escribe código hasta que exista contracts.md.
Ningún humano escribe código hasta que exista contracts.md.

### 1.2 Spec Before Implementation
El qué y el por qué se documentan antes del cómo.
Una historia de usuario sin criterios de aceptación no es una historia — es un deseo.

### 1.3 La IA es el ejecutor, no el arquitecto
La IA escribe código. El humano diseña sistemas.
La IA propone planes. El humano aprueba o rechaza.

### 1.4 El código es desechable, los contratos son permanentes
Si un servicio se reescribe desde cero, los contratos siguen siendo válidos.
Un contrato roto es una emergencia. Un servicio roto es un bug.

### 1.5 Modularidad sobre velocidad
Un agente. Una tarea. Un contrato.
La velocidad que ganas en el prompt la pierdes triplicada en el debugging.

---

## 2. Estructura de documentos CSDD

Todo proyecto CSDD tiene exactamente estos archivos en la raíz:

CONSTITUTION.md   - reglas del juego
contracts.md      - tipos, interfaces, eventos (Fase 1)
spec.md           - historias de usuario, criterios (Fase 2)
plan.md           - arquitectura, estructura (Fase 3)
tasks.md          - tareas atómicas rastreables (Fase 4)
README.md         - para la comunidad

Estos archivos se crean en orden. No se salta ninguno.

---

## 3. Definicion de hecho

Una tarea esta hecha cuando:
- El código compila sin warnings
- Los tests pasan incluyendo casos de error
- El contrato se respeta
- Un humano lo reviso contra la spec

---

Gustavo Osorio — @GustavoOsorioDev
Ingenieria real. Sin vibes.
