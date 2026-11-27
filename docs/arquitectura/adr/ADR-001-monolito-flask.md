# ADR-001-monolito-flask — Monolito modular Flask para el MVP

- **Estado:** Aceptada como base técnica propuesta; confirmar en reunión de equipo/tutor.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

El prototipo actual utiliza Flask y el alcance inicial no justifica microservicios.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

Mantener Flask y extraer límites de presentación, aplicación, dominio e infraestructura de manera incremental.

## Consecuencias

Menor coste de infraestructura y reutilización; exige pruebas y límites de imports claros.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir si los requerimientos confirmados exceden claramente este despliegue o hay equipos/escala que lo exijan.
