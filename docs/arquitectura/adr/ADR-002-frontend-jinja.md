# ADR-002-frontend-jinja — Jinja + JS/CSS progresivo en el MVP

- **Estado:** Aceptada como base técnica propuesta; confirmar en reunión de equipo/tutor.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

La UI actual es una plantilla con interacciones limitadas y la app objetivo es una web responsive.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

Mantener la UI existente, corregir accesibilidad y responsive; no introducir React/SPA sin evidencia.

## Consecuencias

Un stack menos que mantener; interacciones muy complejas u offline podrían justificar SPA en el futuro.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir con requerimiento validado de offline, tiempo real, clientes múltiples o estado frontend complejo.
