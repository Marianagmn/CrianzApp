# ADR-004-rag-y-gobernanza — Corpus cerrado, versionado y con abstención

- **Estado:** Requisito técnico de seguridad; la aprobación clínica/editorial queda pendiente.
- **Fecha:** 2026-10-17
- **Decisores:** equipo de tesis + tutor; validación institucional cuando aplique

## Contexto y problema

El bot responde sobre un programa sensible y el corpus aún requiere revisión de derechos/versión.

## Opciones consideradas

1. Mantener el estado actual.
2. Adoptar la opción recomendada incrementalmente.
3. Adoptar una alternativa de mayor complejidad solo si un requisito la justifica.

## Decisión propuesta

Solo usar documentos y guiones aprobados; citar fuente y versión; abstenerse si falta evidencia; generación remota desactivada hasta aprobación.

## Consecuencias

La cobertura será limitada pero verificable; exige suite dorada, revisión experta, retiradas y rollback.

## Evidencia / supuestos

Auditoría estática del prototipo en `01-auditoria.md`. La auditoría no ejecutó pruebas y no revisó todos los documentos binarios del corpus.

## Revisión

Reabrir solo con evaluación documentada, aprobación profesional y cambios de alcance aprobados.
