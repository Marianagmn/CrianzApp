# ADR-003-postgresql-chroma — Separar persistencia transaccional e índice RAG

- **Estado:** Propuesta; falta validar hosting y stack institucional.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

Cuentas, consentimientos y actividades requieren integridad transaccional; Chroma indexa documentos.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

PostgreSQL como fuente de verdad operativa; Chroma como índice derivado y reconstruible de documentos aprobados.

## Consecuencias

Introduce migraciones/backups; evita tratar vectorstore como base de datos de participantes.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir si plataforma institucional impone otro almacén o evaluación de capacidad revela limitaciones.
