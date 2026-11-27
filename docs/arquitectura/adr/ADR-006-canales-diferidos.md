# ADR-006-canales-diferidos — WhatsApp, SMS, foro y avatar fuera del MVP

- **Estado:** Aceptada como estrategia de alcance propuesta.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

Canales/foro requieren presupuesto, consentimiento, operación y controles adicionales.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

No implementar canales externos ni foro en MVP; estudiar factibilidad tras construir el núcleo seguro. Email opcional posterior.

## Consecuencias

Menos dependencias y riesgo operacional inicial; expansión requiere una decisión nueva.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir con caso de uso, costo, responsable, proveedor, consentimiento y operación aprobados.
