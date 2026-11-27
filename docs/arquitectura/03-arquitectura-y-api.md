# 03. Arquitectura objetivo, dominio, paquetes, API y ADR

## 1. Recomendación de arquitectura

**Monolito modular Flask**, organizado en presentación, aplicación, dominio e infraestructura, desplegado inicialmente como una unidad web y una base de datos relacional PostgreSQL. Los módulos deben tener límites de importación y servicios pequeños por caso de uso. Evitar microservicios: el equipo, la tesis y el alcance MVP no justifican la complejidad operativa distribuida. El RAG puede ejecutarse dentro de un adaptador modular inicialmente, con tareas de indexación fuera de la petición web.

Este es un objetivo, no una descripción del código actual. El código actual mezcla ruta, instancia y composición del RAG en `main.py`; no implementa estas capas. El árbol siguiente es el estado final de destino incremental. No requiere mover todo de una vez: primero crear pruebas de contrato y aplicar extracción vertical por módulo.

## 2. Capas y dependencias permitidas

1. **Presentación (`presentation`)**: Flask app factory, blueprints, formularios/API, autenticación de sesión, CSRF, validación sintáctica, serialización, códigos HTTP y templates. Sin reglas de negocio ni consultas SQL ad hoc.
2. **Aplicación (`application`)**: casos de uso coordinadores; autorización contextual; transacciones; políticas de idempotencia; interfaces/puertos a repositorios, proveedor de correo, reloj y RAG. No depende de Flask ni de Chroma/Groq.
3. **Dominio (`domain`)**: entidades, value objects, estados, invariantes y políticas puras. No contiene ORM, Flask, API del modelo ni lógica clínica no aprobada.
4. **Infraestructura (`infrastructure`)**: SQLAlchemy/migraciones, hashing/sesiones, repositorios, proveedor de identidad si se adopta, Chroma, loaders, cliente LLM, correo/cola, observabilidad y backups.
5. **Contenido y presentación web**: templates Jinja y JavaScript/CSS progresivo para MVP. La API sirve a la propia UI; una SPA no es condición de modularidad.

**Regla de dependencia:** `presentation → application → domain`. `infrastructure` implementa puertos definidos por application y se conecta en composition root. Domain no importa las otras capas. Un módulo no consulta tablas de otro módulo directamente; usa un caso de uso/contrato explícito. Los módulos pueden compartir tipos pequeños y eventos, no acceder a modelos privados entre sí.

## 3. Árbol de paquetes propuesto

```text
src/
  crianzapp/
    __init__.py                 create_app(), composición de dependencias
    config.py                   settings validados por entorno
    presentation/
      web/                      Jinja, formularios, páginas
      api/v1/                   blueprints, DTOs, errores, CSRF/cookie
      auth/                     resolución principal/autenticación
    application/
      identity/                 Register, Login, RecoverAccount
      privacy/                  RecordConsent, WithdrawConsent, RightsRequest
      participants/             CompleteOnboarding, AssignCohort
      program/                  ListSessions, GetContent
      activity/                 SaveCheckIn, RecordPractice, GetProgress
      assistant/                AskFaq, RunGuideStep
      safety/                   CreateSignal, TriageSignal, RequestHumanSupport
      editorial/                Create/Review/Publish/RetireContent
      notifications/            Schedule/Cancel/DeliverReminder
      analytics/                BuildAggregates
      research/                  CreateApprovedExport
      audit/                     RecordSecurityEvent
    domain/
      identity/ privacy/ study/ program/ activity/
      assistant/ safety/ editorial/ notification/ analytics/
    infrastructure/
      db/                        ORM, UoW, migrations, repositorios
      knowledge/                 parser, índice, versionado, búsqueda
      llm/                       cliente y políticas de proveedor
      email/ queue/ audit/       adaptadores externos
    templates/                   UI autenticada y pública
    static/                       CSS/JS propio
migrations/
tests/
  unit/ integration/ authorization/ security/ conversation/ e2e/
```

El paquete puede empezar bajo `src/` aprovechando el layout actual. La inclusión de componentes no implica construir todo en MVP. No se crean clases “Manager” universales, repositorios genéricos mágicos ni objetos que concentren el proceso completo.

## 4. Módulos y responsabilidades

| Módulo | Responsabilidad / datos de los que es dueño | No debe hacer |
|---|---|---|
| Identity & Access | cuenta, credencial/IdP, sesiones, rol, asignaciones de alcance, recuperación | acceder a texto de investigación sin permiso del dominio correspondiente |
| Privacy & Consent | versión/alcance/estado de consentimientos, solicitudes de titulares, políticas de retención | asumir consentimiento por login |
| Participant & Study | ID seudónimo, asignación a estudio/cohorte y respuestas estructuradas mínimas | guardar contacto junto con dataset de análisis |
| Program & Content | cohortes, sesiones, catálogo de contenido, versionado editorial y citas | exponer borradores o publicar sin revisión |
| Activity | check-ins, prácticas realizadas, eventos de progreso | producir diagnóstico o inferencias psicológicas |
| Assistant | recuperar corpus y producir respuesta anclada/abstención | responder tema abierto, decidir prioridad de riesgo o almacenar chat completo |
| Safety & Escalation | flujos de seguridad aprobados, señales, solicitudes/tareas humanas, estados/SLA | diagnosticar, prometer cobertura no disponible, enviar contenido a proveedor sin permiso |
| Notifications | preferencias, quiet hours, intentos/estado de entrega, cancelación | enviar narrativa sensible en asunto o mensaje |
| Analytics | métricas técnicas/uso/adherencia agregadas y supresión | leer identidad individual como administrador técnico |
| Professional Workspace | vista mínima de asignados y campos autorizados | acceso por búsqueda global o perfil IA opaco |
| Research Exports | datasets codificados, diccionario de variables, generación y auditoría de export | juntar tabla de identidad o datos no aprobados |
| Community (futuro) | publicaciones, reportes, sanciones y moderación | activarse sin operadores, SLA y moderación aprobados |
| Audit & Security | eventos de seguridad y acciones administrativas, retención del log | registrar contraseñas, tokens, prompt completo o narrativa por defecto |

## 5. Modelo de dominio y clases candidatas

Solo introducir una clase cuando mantenga identidad, invariantes o comportamiento propio; estructuras de transporte pueden ser DTOs. IDs aleatorios/UUID; evitar ID incremental público. Identidad y dataset analítico se conectan con tabla/servicio restringido y clave de enlace custodiada separadamente.

| Entidad/value object | Campos mínimos candidatos | Invariantes/comportamiento |
|---|---|---|
| `Account` | id, contact_ref, auth_state, created_at, status | cuenta no equivale a consentimiento; bloqueo/revocación invalida sesiones según política. |
| `RoleGrant` | subject_id, role, scope_type/id, valid_from/to | permiso con alcance; no existe rol universal admin. |
| `ConsentRecord` | subject_ref, purpose, version, status, recorded_at, channel, evidence_ref | un registro por finalidad/versión/estado; retirada posterior a aceptación; no sobreescribir historia. |
| `RightsRequest` | requester_ref, request_type, received_at, status, due_by, resolution_ref | acceso, corrección, retiro, supresión/consulta se tramitan, no se resuelven implícitamente. |
| `Participant` | participant_id, study_code, cohort_id, status, enrollment_at | el contacto no vive aquí; enlace a cuenta controlado por Identity. |
| `Study` | study_id, protocol_version, status, retention_policy_ref | solo versión aprobada; cohortes/diccionario bajo protocolo. |
| `Cohort` / `ProgramSession` | cohort_id, session_id, sequence, start/end, status | habilitación/fecha y pertenencia validadas. |
| `ContentItem` / `ContentVersion` | id, version, title, type, body_ref, source_ref, approval, valid dates | versionado inmutable y transición draft→reviewed→published→retired. |
| `PracticeCard` | content_version_id, objective, duration, instructions_ref | instrucciones aprobadas; completar puede registrar evento separado. |
| `CheckIn` | participant_id, instrument_version, responses_structured, submitted_at, status | solo campos autorizados; no asumir escala clínica; revisión/corrección según política. |
| `ActivityEvent` | participant_id, event_type, object_version, occurred_at, source | evento acotado/idempotente; no incluir texto libre innecesario. |
| `KnowledgeDocumentVersion` | id, source, version, approval_ref, checksum, validity, license_ref | RAG consulta solo versiones aprobadas vigentes; rollback auditable. |
| `EvidenceChunk` | document_version_id, chunk_id, page/section, text_ref, embedding_version | cita resuelve a fuente vigente; índice no es fuente de verdad editorial. |
| `AssistantTurn` (opcional) | request_id, mode, outcome, source_ids, model/prompt version, retention class | metadatos mínimos por defecto; sin transcript persistente. Solo añadir transcript con aprobación. |
| `SafetySignal` | id, participant_ref, trigger_type, confidence/category (si aplica), status, assigned_to, created_at | “señal” observable, sin campo diagnóstico/score psicológico; evento auditado y acceso restringido. |
| `SupportRequest` | id, participant_ref, channel, category, status, assigned_to, timestamps | expectativa de servicio explícita; idempotencia y cierre. |
| `NotificationPreference` | participant_ref, channel, enabled, cadence, quiet_hours, locale | opt-in granular y revocable; no envío sin estado actual. |
| `Reminder` / `DeliveryAttempt` | event_ref, template_version, scheduled_at, result, provider_ref | payload minimizado, no contenido sensible; reintento seguro. |
| `AuditEvent` | actor_id/role, action, object_kind/id, time, outcome, request_id | inmutable/controlado, sin secreto ni texto de conversación. |
| `ExportJob` | requester, protocol_scope, variables, filters, status, artifact_expiry, checksum | autorización y propósito; artefacto expira y su descarga se registra. |

No se propone entidad `ChildProfile` con nombre/fecha de nacimiento ni puntuación psicológica. Las respuestas de caracterización deben ser campos cerrados aprobados por protocolo, con la mínima granularidad posible.

## 6. Persistencia e infraestructura

- **PostgreSQL** como fuente de verdad de cuentas, consentimientos, asignaciones, contenido versionado, actividades y solicitudes. SQLAlchemy es opción técnica; debe decidirse en ADR de implementación. Migraciones versionadas y constraints en DB.
- **Chroma existente** puede permanecer como índice interno derivado, no registro oficial ni repositorio de participante. El índice debe referenciar versiones/checksums de documentos, reconstruirse idempotentemente y no hacer ingestión de archivos arbitrarios desde solicitudes web.
- **Cola de trabajos** solo cuando recordatorios, exportaciones o indexación superen proceso web; elegir una solución operable por el equipo. No montar microservicios prematuramente.
- **Storage** para artefactos de export/backups cifrado y con expiración. Separar secretos de archivos y repositorio. Cifrado de disco/DB y TLS según controles de infraestructura que se elija.
- **Configuración** tipada, validada al arranque; dev/test/staging/prod separados. Falla de configuración sensible debe cerrar de forma segura. No usar `debug=True` en producción.
- **Observabilidad** métricas y trazas técnicas con request IDs; excluir texto de consulta, respuesta, identificador directo y secretos por defecto.

## 7. Decisión de frontend

| Opción | Complejidad | Accesibilidad/mantenibilidad | Adecuación a MVP/tesis |
|---|---|---|---|
| Mantener Jinja + JS/CSS progresivo | Menor stack; Flask puede entregar páginas y API propia. | Menos dependencias/duplicación; accesibilidad depende de implementación y pruebas, no del framework. | Recomendado mientras UI son formularios, biblioteca, panel simple y chat sin colaboración en vivo. Facilita centrar tesis en adherencia/IA responsable. |
| React/TypeScript SPA | Node/build, API, estado, routing y despliegue por separado o integración; mayor costo inicial. | Buenas herramientas tipadas/estado, pero no hacen UI accesible automáticamente; requiere pruebas y disciplina. | Considerar si offline/tiempo real, interacciones ricas, clientes múltiples o equipo frontend dedicado lo justifican. No hay esa evidencia ahora. |

**Recomendación:** mejorar interfaz actual y mantener UI same-origin con Flask en MVP. Introducir TypeScript por incremento solo si la complejidad demostrable excede templates, no como refactor cosmético. Evitar mantener dos frontends.

## 8. Contratos de API propuestos

Prefijo `/api/v1`; JSON UTF-8; fechas RFC 3339 UTC; UUID; esquema OpenAPI versionado en implementación; errores uniformes `{ "error": {"code":"...", "message":"...", "request_id":"..."}}`. Nunca incluir stack traces, datos de otros participantes ni detalle que permita enumeración. Autenticación preferida para la propia UI: sesión segura same-site + CSRF para operaciones mutativas; bearer token solo si existe cliente no navegador y con gestión específica. `Idempotency-Key` en operaciones de escritura externas/reintento.

| Método/ruta | Actor | Solicitud/respuesta conceptual | Validación/permiso |
|---|---|---|---|
| `POST /auth/register` | Visitante | email/invitación/credencial → cuenta y verificación pendiente | rate limit, respuesta neutra; no crea consentimiento de investigación |
| `POST /auth/login`, `POST /auth/logout` | Usuario | credencial → sesión / revocación | cookie Secure, HttpOnly, SameSite; CSRF; throttling |
| `POST /auth/recovery` | Visitante | contacto → mensaje neutro | token de un uso y expiración |
| `GET /me`, `GET /me/consents`, `POST /me/consents` | Participante | consultar estado/aceptar o retirar versión y finalidad | titular autenticado; versión activa; auditoría |
| `GET/PUT /me/onboarding` | Participante | esquema de campos cerrados aprobado → estado | solo datos autorizados; validación por versión |
| `GET /program/sessions`, `GET /content` | Participante | filtros cohort/topic/session/kind → catálogo publicado | filtrar por asignación y vigencia |
| `GET /content/{content_id}` | Participante | detalle/versión/cita → recurso | no revelar borrador ni contenido de otra cohorte |
| `POST /assistant/faq` | Participante | `{question, request_id}` → `{answer, mode, citations[], abstained, notice}` | límite de texto, rate/quota, consentimiento, redacción, corpus cerrado; no history controlado por cliente por defecto |
| `GET /guides/{guide_id}`, `POST /guides/{guide_id}/steps` | Participante | estado y opción → siguiente paso con versión | flujo publicado; sin generación libre |
| `POST /check-ins`, `POST /practices/{id}/completion` | Participante | respuesta/evento/idempotency key → estado | asignación, ventana, consentimiento, reglas del instrumento |
| `POST /support-requests` | Participante | categoría/canal mínimo → `request_id`, horario/expectativa | autorización, consentimiento/contacto, límites de spam; sin promesa de crisis |
| `GET /professional/participants/{id}/summary` | Psicólogo/facilitador | campos estructurados limitados → resumen auditable | asignación activa + permisos por campo; deny cross-participant |
| `GET /admin/metrics` | Admin técnico/coordinador | cohorte/periodo → agregados y supresión | permiso específico; sin filas individuales ni respuestas de onboarding |
| `POST /research/exports` / `GET /research/exports/{id}` | Investigador autorizado | protocolo, variables, filtros → job/artefacto temporal | aprobación/alcance, seudonimización, audit log, expira |
| `POST /editorial/content`, `POST /editorial/content/{id}/review`, `/publish`, `/retire` | Editor/validador | metadatos, fuente, cuerpo → versión/estado | separación de funciones; fuente/licencia requeridas |
| `PUT /me/notification-preferences` | Participante | canal/frecuencia/horas → preferencia | opt-in granular, revocación inmediata |
| `GET /health/live`, `GET /health/ready` | Operación | estado técnico mínimo | no exponer número de documentos/modelo/variables si no necesario; endpoint vivo separado de readiness |

### API de FAQ: respuesta segura sugerida

`mode` ∈ `{extractive, grounded_generation, abstain, unavailable, safety_flow}`. `citations` contiene `{document_id, version, title, page?, section?, chunk_ref}`; no incluye IDs de participante. `abstained=true` si falta evidencia; respuesta enumera alternativa segura. Diferenciar `unavailable` de `abstain`. No registrar `question`/`answer` en logs ni guardar historial por defecto. En caso de transmisión externa, se bloquea hasta aprobación institucional y control de contenido enviado.

## 9. ADR — decisiones de arquitectura

### ADR-001 — Monolito modular Flask (propuesto; estado: aceptar para MVP)

- **Contexto:** prototipo Flask existente, alcance MVP aún acotado y trabajo individual/tesis.
- **Decisión:** conservar Flask como backend inicial, añadir límites de módulo y capas; no migrar framework ni microservicios ahora.
- **Consecuencias:** menor costo y reaprovechamiento; requiere disciplina de imports, transacciones, pruebas y evitar `main.py` como composición global creciente. Reabrir solo con evidencia de necesidades de escala/organización incompatibles.

### ADR-002 — Jinja/JS antes que React (propuesto; estado: aceptar para MVP)

- **Contexto:** frontend actual ya es plantilla web con interacciones discretas y sin cliente móvil/SPA requerido.
- **Decisión:** mantener templates y mejorar accesibilidad/responsive; API interna versionada.
- **Consecuencias:** menor carga de build/dependencias; TypeScript/React requiere ADR posterior si hay offline, tiempo real o complejidad de estado medible.

### ADR-003 — Persistencia relacional operativa separada de índice RAG (propuesto; estado: pendiente de confirmar stack institucional)

- **Contexto:** el vectorstore es solo conocimiento; la futura plataforma necesita relaciones transaccionales, consentimiento y auditoría.
- **Decisión:** Postgres como fuente de verdad; Chroma como índice derivado privado.
- **Consecuencias:** migraciones, backups/restores, controles de DB y segregación; seleccionar hosting tras revisar retención, región y acuerdos.

### ADR-004 — Corpus cerrado, aprobación humana y abstención por defecto (propuesto; estado: requisito de seguridad)

- **Contexto:** chatbot vinculado a un programa sensible y materiales entregados aún no revisados aquí.
- **Decisión:** solo fuente oficial aprobada/versionada; FAQ aporta citas; guías inmediatas deterministas; generación limitada y apagada hasta superar evaluación/acuerdo.
- **Consecuencias:** más curaduría; cobertura deliberadamente limitada; requiere suite dorada, revisión profesional, red-team y mecanismo de retirada.

### ADR-005 — No persistir conversaciones completas por defecto (propuesto; estado: aceptar para MVP)

- **Contexto:** mensajes pueden incluir datos sensibles y de menores; el protocolo aún no define necesidad de transcript.
- **Decisión:** guardar evento mínimo, modo, fuente/versión, resultado y estado, no el texto de pregunta/respuesta. Si protocolo necesita contenido, aprobación explícita y nueva evaluación de riesgos.
- **Consecuencias:** menor exposición y menor capacidad de auditoría semántica; usar evaluación sintética/anónima y métricas agregadas.

### ADR-006 — Canales diferidos (propuesto; estado: no incluir)

- **Contexto:** correo, SMS, WhatsApp y foro tienen implicaciones de consentimiento, proveedor, costo, operación y moderación.
- **Decisión:** MVP solo puede considerar email transaccional no sensible después de aprobación; WhatsApp/SMS y foro requieren informe de factibilidad/gate.
- **Consecuencias:** menor complejidad; recordatorios no esenciales no deben bloquear MVP; toda expansión tendrá ADR propio.
