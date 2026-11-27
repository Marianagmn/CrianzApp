# 06. Cómo empezar y mantener la documentación

## 1. Primero: congelar el estado actual

1. Crear una rama `docs/architecture-baseline` y copiar los documentos de esta carpeta a `docs/arquitectura/` dentro del repositorio. No modificar `main.py` ni servicios del chatbot en este paso.
2. Guardar `git status`, `git rev-parse HEAD`, versión de Python, sistema operativo y dependencias instaladas. Registrar el commit exacto de la auditoría.
3. Crear un tag o commit de referencia del prototipo, después de revisar que no contenga secretos ni datos reales. No borrar el historial.
4. Ejecutar y registrar baseline de pruebas con los comandos documentados en README/CI. La auditoría recibida no los ejecutó, por lo que aún no hay baseline verde demostrado.
5. Ejecutar el chatbot localmente con proveedor externo deshabilitado y datos sintéticos; verificar manualmente rutas, citas, abstención, errores y tamaño móvil.
6. Leer el contenido de todos los PDF, DOCX, MD y notebook usados por el corpus; revisar autoría, permisos/licencias, versión, vigencia y aprobación por la especialista. La auditoría no leyó íntegramente esos archivos.

## 2. Qué se puede hacer antes de que la cliente responda

Estas tareas no requieren inventar las preguntas del estudio ni almacenar participantes:

- Reproducibilidad del entorno y dependencias.
- Tests actuales con informe de qué pasa/falla.
- Configuración por ambiente y app factory en un corte pequeño, solo después de tests de contrato.
- Separar dependencias de runtime y evaluación.
- Corregir estados UI `unavailable` vs `abstain` y conservar privacidad/ayuda visibles en móvil.
- Diseñar y probar el pipeline offline de corpus aprobado y sus metadatos usando contenido sintético o material autorizado.
- Crear el esqueleto de módulos/interfaces y migraciones vacías solo si el equipo acepta ADR y no obliga a elegir campos de investigación prematuramente.
- Escribir pruebas de modelo de permisos con usuarios sintéticos, sin habilitar rutas para participantes reales.
- Mantener un inventario de requisitos y riesgos con responsable/estado.

## 3. Qué no se debe implementar hasta cerrar requisitos

No inventar ni crear todavía: preguntas/campos del onboarding, respuestas de caracterización, formularios de check-in, objetivos numéricos de adherencia, reglas reales de escalamiento, textos de crisis, exportaciones de datos, paneles con información individual, retención de registros, mensajes de email/SMS/WhatsApp o integración con proveedores generativos con texto de usuarios. Se puede implementar la infraestructura genérica después de aprobar el modelo de permisos y usar datos sintéticos.

## 4. Primeros cinco incrementos técnicos

### Incremento 0 — baseline

Entregables: inventario confirmado, requisitos y dependencias reproducibles, reporte real de tests, errores conocidos, capturas de la UI, corpus enumerado y snapshot/tag del prototipo.

**Aceptar cuando:** otro miembro del equipo puede crear el entorno desde instrucciones, ejecutar tests sin credenciales reales y reproducir el chatbot local.

### Incremento 1 — aplicación configurable y pruebas

Crear `create_app(config)` y composición controlada de dependencias; retirar la instancia pesada de RAG del import global; settings validados y separados por entorno; distinguir fallo de configuración, fallo de recuperación y proveedor no disponible. Mantener endpoint de demo hasta probar contrato.

**Aceptar cuando:** tests no llaman a internet, el servicio inicia con proveedor desactivado, health/live y ready tienen propósito claro y los errores no filtran traceback.

### Incremento 2 — contenido gobernado

Separar importación/indexación de la petición web; registrar documento fuente, checksum, permiso/licencia, versión, aprobación y estado; solo índice aprobado visible; reconstrucción reproducible.

**Aceptar cuando:** se puede publicar, retirar y reconstruir una versión con evidencia; las respuestas se vinculan a fuente y no se utiliza contenido no aprobado.

### Incremento 3 — persistencia y seguridad base

Tras aceptar ADR de PostgreSQL/SQLAlchemy, añadir migraciones, cuentas/roles/scopes en entorno de prueba, CSRF/sesiones/rate limiting y tests negativos por objeto. El consentimiento de investigación real todavía debe esperar textos y protocolo aprobados.

**Aceptar cuando:** pruebas demuestran que la cuenta A no puede consultar recursos de B, los logs no incluyen texto sensible y la restauración de base de datos se ensaya con datos sintéticos.

### Incremento 4 — primer recorrido vertical del programa

Solo con preguntas, contenido y flujo aprobados: inscripción → consentimiento relevante → onboarding → biblioteca → práctica/check-in → métricas agregadas. Cada flujo con criterios de aceptación, pruebas y auditoría.

**Aceptar cuando:** toda pantalla/endpoint tiene permiso, estado de error, fuente de dato, evento mínimo y pruebas; no se depende de campos adivinados.

## 5. Cómo organizar el backlog

Usar GitHub Issues, GitLab Issues o un tablero único con estas etiquetas:

- `type:requirement`, `type:bug`, `type:security`, `type:documentation`, `type:research`, `type:decision`.
- `priority:P0`, `P1`, `P2`, `P3`.
- `status:needs-client`, `status:needs-research`, `status:ready`, `status:in-progress`, `status:blocked`, `status:done`.
- `module:identity`, `module:content`, `module:assistant`, etc.

Cada issue debe enlazar a un RF/HU/ADR, describir criterio Given/When/Then, prueba, riesgo, responsable y estado de la dependencia. Un issue `needs-client` no debe etiquetarse `ready`.

## 6. Proceso Git recomendado

- `main`: versión estable y demostrable; ramas protegidas.
- `feature/<rf-or-short-name>`: un cambio acotado por PR.
- `docs/<topic>`: documentación aislada.
- `fix/<bug>`: corrección asociada a issue.
- PR obligatorio con resumen, RF/HU relacionados, pruebas ejecutadas, riesgos/privacidad, capturas cuando aplique y migración/rollback si hay DB.
- No mezclar una refactorización masiva con funcionalidades nuevas. No meter archivos `.env`, bases reales, conversaciones ni exportaciones en Git.

## 7. Estructura final de documentación dentro del repositorio

```text
docs/
  arquitectura/                # este paquete
    00-LEEME.md
    00-diseno-base-v1.md
    01-auditoria.md
    02-especificacion-funcional.md
    03-arquitectura-y-api.md
    04-datos-privacidad-ia-seguridad.md
    05-roadmap-validacion.md
    06-como-empezar.md
    07-matriz-de-pendientes.md
    adr/
      ADR-template.md
      ADR-001-monolito-flask.md
      ADR-002-frontend-jinja.md
      ADR-003-postgresql-chroma.md
      ADR-004-rag-y-gobernanza.md
      ADR-005-no-transcripciones-por-defecto.md
      ADR-006-canales-diferidos.md
    plantillas/
      acta-validacion-requisitos.md
  base_conocimiento/            # corpus y fuentes originales, no sobreescribir sin control
  flujo_completo_rag.md         # documentación original existente
```

No elimines docs actuales; enlázalos y aclara cuál es la versión vigente. Usa fechas ISO (`YYYY-MM-DD`) y evita nombres `final-final-v3`.

## 8. Ritmo de mantenimiento documental

- Cada PR cambia documentación en el mismo cambio si altera API, esquema, métricas, seguridad o comportamiento de IA.
- Cualquier elección con alternativas y consecuencias merece ADR. No reescribir una ADR aceptada: crear otra que la sustituya.
- Revisar matriz de pendientes una vez por reunión con cliente/investigadora.
- Actualizar el árbol y líneas de auditoría después de refactorizar.
- Versionar corpus/prompt/dataset de evaluación y registrar hash, responsable, fecha y resultados.
- Guardar evidencia de pruebas, pero nunca anexar información personal real sin autorización y proceso de anonimización aprobado.
