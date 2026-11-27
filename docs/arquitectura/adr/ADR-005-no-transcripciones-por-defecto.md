# ADR-005-no-transcripciones-por-defecto — No persistir conversaciones completas por defecto

- **Estado:** Aceptada como base de minimización propuesta; protocolo debe confirmarlo.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

Las consultas podrían incluir datos de salud/familiares o información identificable de menores.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

Guardar metadatos mínimos para operación/evaluación solo si están aprobados; no guardar texto completo en DB/logs por defecto.

## Consecuencias

Menos riesgo de exposición, menos capacidad de revisar el lenguaje exacto después.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir solo si protocolo aprobado requiere transcript y establece acceso, cifrado, retención, finalidad y autorización.
