# 05. Roadmap, métricas, NFR, pruebas, DoD y trazabilidad

## 1. Roadmap por fases y gates

Esfuerzos aproximados de ingeniería en persona-semanas (PS), no calendario; requieren ajuste por equipo, tutor, proveedor, comité y alcance exacto. Una semana de aprobación ética/legal no se representa como esfuerzo de ingeniería. No empezar con datos reales hasta completar los gates P0.

| Fase | Alcance | Criterios de salida/gate | Esfuerzo estimado |
|---|---|---|---:|
| 0 — Decisiones institucionales y baseline | Acordar protocolo, responsable/encargado, variables, consentimientos, retención, escalamiento, corpus/licencias, hosting, población, métricas y alcance. Ejecutar y guardar baseline actual de tests/seguridad/RAG en copia controlada. | Acta de decisiones; matriz de datos aprobada; revisión ética/jurídica iniciada/completada según protocolo; guiones y capacidad humana; no usar datos reales todavía. | 2–4 PS ingeniería + tiempos institucionales |
| 1 — Núcleo seguro | Flask modular/app factory, configuración y dependencias reproducibles; PostgreSQL/migraciones; identidad, roles/scope, consentimientos, derechos; audit event mínimo; entornos y backups; UI de acceso. | Tests de permisos negativos; restauración; revisión ASVS acordada; consentimientos separados/versionados; dataset de prueba sintético; aprobación de privacidad. | 5–9 PS |
| 2 — Programa digital básico | Cohortes/sesiones, catálogo editorial, tarjetas prácticas, onboarding mínimo y check-in/progreso descriptivo. | Flujos aprobados y versionados; aislamiento por cohorte; accesibilidad básica auditada; métricas funcionales; pruebas end-to-end. | 4–7 PS |
| 3 — Asistente acotado y seguridad humana | FAQ RAG corpus cerrado, citas/abstención/evaluación; guía inmediata preaprobada; solicitud y cola humana; vista profesional mínima para asignados con campos aprobados; estados de fallo. Generación solo tras decisión. | Suite dorada y pruebas adversariales superan umbrales ratificados; revisión profesional; responsable/horario/fuera de horario; proveedor/transferencia aprobados o proveedor apagado. | 4–8 PS, más iteraciones de evaluación |
| 4 — Piloto controlado | Pilotaje pequeño y supervisado con métricas aprobadas, capacitación, procedimiento de incidentes, soporte y retirada. Recordatorios por email si se aprobaron. | Gate de comité, operación, seguridad, protección de datos y disponibilidad; ensayo de restauración y rollback; métricas y criterios de parada definidos. | 3–6 PS + operación |
| 5 — Evaluar/expandir | Evaluación de viabilidad/adherencia, ajustes, factibilidad SMS/WhatsApp, perfil profesional ampliado; decisión de escalamiento. | Resultados interpretados según protocolo, riesgos/reidentificación actualizados, decisión y presupuesto. | 3–6 PS por paquete |
| Futuro, no MVP | Foro moderado, simulador complejo, role-play/avatar, analítica predictiva/perfil IA. | ADR, evidencia/ética, presupuesto, moderación/operación y red-team propios. | Estimar solo tras aprobación |

### Roadmap de conservación del prototipo

1. Mantener Flask/Jinja y extracción/citas actuales como base, sin despliegue de datos reales.
2. Congelar fixtures y registrar versiones; corregir estados de error; modularizar por cortes verticales.
3. No trasladar historial de demo a persistencia de producción sin revisión de privacidad.
4. Extraer RAG a puerto/servicio; corpus actual se trata como candidato, no aprobado por el solo hecho de estar en Git.
5. Elegir host/storage institucional y verificar su configuración antes de almacenar participantes.

## 2. Métricas definidas

Publicar numerador, denominador, ventana, exclusiones, versión de consulta y fecha de extracción junto con cada métrica. Datos faltantes no se cuentan como cero. Desglosar por cohorte solo si el protocolo permite y se protege reidentificación. No usar métricas para atribuir eficacia clínica o causalidad sin diseño de estudio.

### 2.1 Métricas técnicas

| Indicador | Fórmula/ventana | Fuente | Limitaciones/objetivo candidato |
|---|---|---|---|
| Disponibilidad HTTP | solicitudes health/función completadas correctamente ÷ solicitudes elegibles, por mes; excluir mantenimiento anunciado según política | Monitor externo y app metrics, sin payload | Medir uptime del servicio, no que RAG sea correcto. Objetivo candidato ≥99.5% mensual tras piloto; ratificar. |
| Latencia p95 página/contenido | p95 de duración en servidor y cliente, semanal | Métricas técnicas | Excluir/segmentar llamadas proveedor. Candidato: API catálogo p95 ≤800 ms con carga acordada. |
| Latencia FAQ local | p95 desde petición hasta resultado, semanal | Métricas, versión de índice | Objetivo candidato ≤3 s bajo escenario de prueba; hardware/corpus afectan. |
| Errores 5xx y dependencia | 5xx ÷ peticiones; fallos externos ÷ llamadas, diario/semanal | Logs técnicos redacted | No loggear texto; separar timeout de abstención. |
| Recuperación | tiempo medido para restablecer DB/index/artefacto en simulacro | Acta de restore | Objetivos propuestos RPO ≤24 h / RTO ≤8 h, deben acordarse según riesgo y capacidad. |
| Incidentes/permisos | intentos denegados, accesos excesivos, secretos detectados | Auditoría segura | Interpretar por usuarios/pruebas; no objetivo de “cero intentos”. |

### 2.2 Métricas agregadas de uso

| Indicador | Definición |
|---|---|
| Entradas autenticadas por semana | Conteo de eventos de inicio de sesión válidos por semana; no equivale a personas únicas ni a adherencia. El panel técnico solo muestra agregado. |
| Usuarios únicos activos por semana | Conteo agregado de IDs seudónimos distintos con evento válido; aplicar supresión de celdas pequeñas y permisos por cohorte. |
| Actividades por tipo y estado | Conteos agregados de vistas/inicios/completados por tipo de actividad y periodo; separar denominadores y no mostrar respuestas ni texto individual. |
| Activación | participantes con primer acceso y paso onboarding requerido completado dentro de ventana ÷ participantes elegibles con invitación válida en esa ventana. Acordar si el consentimiento de investigación es condición de elegibilidad. |
| Participantes activos | IDs seudónimos distintos con ≥1 evento de actividad definido durante semana/mes ÷ elegibles activos en la misma ventana. No usar sesión/login solo como adherencia. |
| Uso de biblioteca | participantes con ≥1 apertura de contenido por ventana ÷ elegibles activos; además conteo de aperturas, deduplicadas según regla. |
| Preguntas FAQ | conteo de peticiones por modo/resultado/ventana; no almacenar query raw; separar abstain, unavailable y safety. No interpretar volumen como beneficio. |
| Solicitud humana | solicitudes creadas y cerradas por categoría/periodo; acceso restringido, cohortes pequeñas suprimidas. |
| Entrega de recordatorio | mensajes aceptados por proveedor ÷ mensajes intentados con preferencia válida; reportar rebotes aparte. No prueba que la persona leyó. |

### 2.3 Métricas descriptivas de adherencia

Definir con investigadora y protocolo: unidad de adherencia (participante, sesión o actividad), periodo (semana/sesión/total) y evento válido. Candidatos, no variables aprobadas:

- **Adherencia a sesiones:** sesiones marcadas como completadas dentro de la ventana ÷ sesiones asignadas/elegibles. No contar futuras, no habilitadas o retiradas.
- **Finalización de actividad:** actividades completadas y registradas ÷ actividades asignadas y elegibles en la ventana.
- **Retención de programa:** participantes con evento válido en semana $n$ ÷ participantes elegibles con inicio en cohorte; reportar censura/retirada y no respuesta por separado.
- **Persistencia:** participantes con evento en semanas consecutivas definidas ÷ elegibles con seguimiento completo en esas semanas.
- **Exposición a apoyo digital:** número de vistas/check-ins/FAQ por participante/periodo; informar distribución y cero uso, no presentar como adherencia automáticamente.

No afirmar que recordatorios/IA causaron mejor adherencia sin diseño causal, grupo/comparador y análisis aprobados. La métrica de sistema no reemplaza resultado de investigación.

### 2.4 Variables de investigación

Solo definir tras protocolo: outcomes primarios/secundarios, escalas y versiones, tiempo basal/post, covariables, criterios de elegibilidad, manejo de faltantes, subgrupos, plan estadístico, tamaño muestral y seudonimización. Variables de investigación no son analítica operativa; acceso/exportación y finalidad deben estar autorizados. El documento no inventa instrumentos ni resultados.

### 2.5 Calidad RAG/IA

| Indicador | Fórmula y conjunto | Criterio de liberación propuesto (ratificar) |
|---|---|---|
| Citas válidas | respuestas con cada cita que resuelve a pasaje/versión vigente ÷ respuestas que citan | 100% en suite automatizada de integridad de cita; revisión adicional humana. |
| Soporte por evidencia | enunciados sustantivos juzgados soportados ÷ enunciados revisados por profesionales | Meta candidata ≥95% en benchmark de liberación; especificar muestreo y acuerdo entre revisores. |
| Abstención fuera de corpus | consultas OOD con `abstain` ÷ casos OOD del conjunto | Meta candidata ≥95%; falsos aceptados se analizan manualmente. |
| Abstención en dominio | consultas de dominio respondibles que no se abstienen ÷ casos respondibles | Medir recall/utilidad junto a falsos positivos; no optimizar a costa de inventar. |
| Fuga de instrucciones / PII | ataques que logran comportamiento prohibido o exponen secreto/dato sintético | Cero severidad crítica en suite acordada; no equivale a garantía de ausencia. |
| Calidad conversacional | utilidad, claridad, tono, riesgo, legibilidad evaluados por rúbrica profesional | Umbrales por criterio definidos antes de ejecutar y reportados con versión. |
| Deriva | cambio en indicadores entre versiones corpus/prompt/modelo | Release bloqueado si supera límites; revalidar cada cambio de modelo/prompt/corpus. |

Ragas (faithfulness, answer relevancy, context precision/recall) se puede reportar como indicador exploratorio con modelo juez/versiones/costos documentados. No traducir una métrica Ragas a “seguro”, “sin alucinaciones” ni eficacia de crianza.

## 3. Requisitos no funcionales medibles (objetivos a ratificar)

| ID | Requisito/meta candidata | Verificación |
|---|---|---|
| RNF-SEC-01 | Mapear controles aplicables de OWASP ASVS, acordar versión/nivel objetivo con asesor; objetivo recomendado como mínimo perfil ASVS nivel 2 por datos sensibles, sin declarar conformidad hasta auditoría. | Checklist con evidencia, revisión independiente y resolución de findings críticos/altos. |
| RNF-SEC-02 | 100% de rutas no públicas con autenticación + autorización de objeto/acción; cero acceso a otro participante en suite negativa. | Tests de RBAC/ABAC/BOLA para todas rutas, revisión de endpoints. |
| RNF-SEC-03 | Ninguna clave/secreto en git, artefactos o logs; rotación/revocación ensayadas. | Secret scan, SBOM/dependency scan en CI; prueba rotación. |
| RNF-PRIV-01 | Conversación raw no persistida ni enviada al proveedor por defecto; toda excepción tiene propósito, consentimiento/aprobación y retención. | Inspección de DB/log/egress en entorno sintético y test de política. |
| RNF-PRIV-02 | Datos admin técnico agregados; export de investigación codificado; tabla identidad separada y con acceso explícito. | Prueba de esquema y permisos + revisión reidentificación con protocolo. |
| RNF-A11Y-01 | Objetivo WCAG 2.2 AA para flujos esenciales: registro, consentimiento, FAQ, check-in, solicitud de apoyo. | Auditoría automática + manual teclado/lector/zoom/contraste; cero bloqueador de criterio WCAG en journeys. No se afirma actual. |
| RNF-UX-01 | Flujo crítico utilizable con viewport 320px, zoom 200%, teclado y lector; privacidad y ayuda visibles en móvil. | E2E responsive en viewports y revisión manual. |
| RNF-PERF-01 | Catálogo p95 ≤800ms y FAQ extractiva p95 ≤3s bajo carga/corpus/equipo definidos. | Test de carga con resultados y hardware/semillas documentados; LLM medido aparte. |
| RNF-AVAIL-01 | Disponibilidad mensual ≥99.5% luego de piloto y proceso comunicado de mantenimiento, si se aprueba presupuesto/capacidad. | Sonda externa y reporte mensual. |
| RNF-REC-01 | RPO ≤24h, RTO ≤8h (propuesta) o valores acordados por protocolo; restore periódico. | Simulacro documentado y cronómetro; restaurar DB, metadatos, documentos y revocaciones. |
| RNF-MAINT-01 | Todas las migraciones reversibles o con rollback explícito; type/lint/test/secret/dependency CI; ningún módulo accede tablas ajenas. | CI y revisión de arquitectura. Cobertura no es criterio único. |
| RNF-OBS-01 | Request ID y métricas técnicas; logs no incluyen query/answer, credencial, datos personales o prompt completo por defecto. | Tests de captura de logs y muestreo redacted. |
| RNF-RAG-01 | Corpus aprobado/versionado, citas 100% resolubles en suite, umbral de abstención ratificado por evaluadores, rollback de versión en <30 min (propuesta). | Suite de regresión/administración, simulacro de retiro y evaluación profesional. |

Metas numéricas son candidatas para especificación y presupuesto; no resultados observados. Ajustarlas antes del MVP de acuerdo con infraestructura, experiencia de usuario, tamaño del estudio y equipo.

## 4. Estrategia de pruebas

| ID | Tipo y cobertura | Herramientas/entorno sugeridos | Gate |
|---|---|---|---|
| T-UNIT | Dominio: consentimiento, estados, scopes, cohortes, eventos, cálculos métricos, política de abstención | pytest, fixtures sintéticos | deterministas, sin red externa |
| T-INTEGRATION | Repositorios DB/migraciones, Chroma corpus versionado, integración app factory, mail mock, jobs idempotentes | PostgreSQL test, proveedor mock | aislamiento y rollback comprobados |
| T-API | OpenAPI/contrato, schema, códigos, validación, error/unavailable, límites y versionado | Flask test client | contrato versionado y no fuga de stack |
| T-AUTHZ | Matriz positiva/negativa por rol y scope; BOLA/IDOR; consentimiento revocado | cuentas A/B sintéticas | cero accesos cruzados |
| T-SEC | OWASP ASVS elegido, CSRF, sesión, rate limit, XSS, secretos, headers, abuso y dependencias | herramientas aprobadas + revisión | findings críticos/altos cerrados o riesgo firmado; no certificar en automático |
| T-PRIV | Inventario y retención, logs redacted, payload a proveedor, derechos, export, supresión, backups | datos sintéticos únicos | verificar no persistencia/envío por defecto y propagación de solicitudes |
| T-CONV | suite in-domain/OOD, citas, umbrales, conflicto/retirada, proveedores caídos/JSON malformado, idiomas/ambigüedad | evaluación offline, revisión profesional | valores de RF-08/RNF-RAG alcanzados antes de activar generación |
| T-SAFETY | Guiones y todas ramas; negación, contexto histórico, typo, falso positivo/negativo; prueba de escalamiento y fuera de horario | escenarios redactados, profesional | 100% mensajes y contactos verificados; proceso humano ensayado |
| T-RESP | 320, 360, 390, 768, 1024+ px, zoom/teclado virtual/orientación | Playwright + dispositivos/revisión | sin pérdida de contenido/acción crítica |
| T-A11Y | Keyboard only, foco, labels, headings, errores, live regions, contraste, reduced motion, lector, zoom/reflow | axe u otro scanner + manual | flujos esenciales revisados; issues rastreados |
| T-LOAD | carga de FAQ/catálogo, concurrencia DB, límites, degradación proveedor | entorno aislado | p95/cupo y costo dentro de límites aprobados |
| T-RESTORE | DB, contenido, vectorstore/rebuild, export temporal, bajas/tombstones | staging aislado | RPO/RTO ratificados cumplidos |
| T-E2E | registro→consentimiento→onboarding→contenido→práctica/check-in→apoyo, más revocación | test data sintética | trazabilidad a journey y capturas sin datos reales |

## 5. Definition of Done (DoD)

Una funcionalidad no está “terminada” hasta que: (1) requisito/historia tiene ID y criterio aceptado por stakeholder; (2) riesgo y dueño de datos identificados; (3) autorización backend evaluada por objeto y scope; (4) cambios de esquema migrados y documentados; (5) pruebas unitarias/integración/negativas/interfaz pertinentes pasan en CI, con resultados adjuntos; (6) entradas, errores, límites, idempotencia y estados offline diseñados; (7) no se registran secretos/transcripts ni datos no necesarios; (8) accesibilidad/responsive manual y automatizada cubierta en journey; (9) logs/alertas no exponen datos y existe procedimiento operacional; (10) traducciones/textos profesionales versionados y aprobados; (11) backup/retención/derechos/cambios terceros considerados; (12) README/ADR/OpenAPI/diccionario y trazabilidad actualizados; (13) no hay findings críticos/altos abiertos o existe aceptación institucional documentada; (14) despliegue/reversión probado en staging y se preservan controles de consentimiento.

## 6. Matriz de trazabilidad necesidad → requisito → módulo → indicador → prueba

| Necesidad de partida | RF | Módulos | Indicadores | Pruebas principales |
|---|---|---|---|---|
| Web responsive móvil | RF-01,06,08,11 | presentation, content, activity | RNF-UX, RNF-A11Y | T-RESP/T-A11Y/T-E2E |
| WhatsApp auxiliar; correo/SMS a evaluar | RF-18/24 | notification/privacy | entrega, opt-in/revocación | T-NOTIFY/T-PRIV |
| Conversación híbrida y contenido oficial | RF-07–09,17 | assistant/editorial/program | citas, abstención, soporte | T-CONV/T-SAFETY/T-CONTENT |
| Atención humana complementaria | RF-10/13/15 | safety/professional | solicitudes/tiempo operativo, con alcance autorizado | T-SAFETY/T-AUTHZ |
| Registro/login/roles | RF-01/02 | identity/access | activación, errores acceso | T-AUTHZ/T-API/T-SEC |
| Onboarding mínimo/consentimiento | RF-03/04/20 | privacy/participant | completion y no respuesta según protocolo | T-CONSENT/T-PRIV |
| Check-ins y seguimiento | RF-11/12 | activity/program | adherencia definida | T-UNIT/T-INTEGRATION/T-E2E |
| Biblioteca/tarjetas | RF-06/07/17 | content/program | uso de biblioteca | T-CONTENT/T-A11Y |
| Métricas agregadas | RF-14/19 | analytics | métricas técnicas/uso/adherencia separadas | T-AGG/T-PRIV |
| Vista profesional mínima estructurada (sin perfil IA) | RF-15 | professional | solo uso autorizado | T-AUTHZ/T-PRIV |
| Export investigación | RF-16 | research/privacy | variables protocolizadas | T-EXPORT/T-PRIV |
| Foro futuro | RF-22 | community | moderación solo si se aprueba | Gate futuro, no aplica MVP |
| Protección de datos colombiana | RF-03/20/21 | privacy/audit/security | solicitudes/ciclo de vida | revisión jurídica + T-PRIV/T-RESTORE |

## 7. Fases de revisión y evidencias de tesis

Guardar como anexos de evaluación: protocolo/actas; baseline de tests; decisiones ADR; versiones del corpus y prompt; conjunto de evaluación anonimizado/sintético; resultados completos con intervalos/limitaciones; log de aprobaciones; casos de seguridad; pruebas de accesibilidad; plan/acta de restauración; métricas con fórmulas reproducibles; incidencias y cambios. No convertir logs personales ni transcripciones en anexos sin autorización y anonimización robusta.

## 8. Preguntas pendientes para decidir con cliente, investigadora y tutor

### Cliente/programa
1. ¿Cuál es la población operativa exacta (primera infancia y rango de edad), región, modalidad, cohortes, duración y sesiones? El README actual refiere 0–12 años, distinto del contexto inicial de primera infancia: confirmar y alinear.
2. ¿Qué contenidos y guiones son oficiales, quién tiene derechos, quién puede aprobar y cada cuánto se revisan?
3. ¿Qué modos entran en primera versión: FAQ, guía inmediata, check-in motivacional? ¿Qué queda expresamente fuera?
4. ¿Qué canal de apoyo humano existe realmente, responsables, horario, tiempo objetivo, suplencias y mensajes si no hay cobertura?
5. ¿Email es obligatorio u opcional? ¿WhatsApp/SMS solo recordatorios o se espera recepción de casos? ¿Qué coste/proveedor/opt-in?
6. ¿Hay necesidad operacional de dashboard profesional y de qué campos exactos? ¿Quién mantiene asignaciones?

### Investigadora / comité / asesoría jurídica
1. ¿Quién es responsable y encargado del tratamiento? ¿Qué instituciones/proveedores están involucrados y dónde se alojan datos?
2. ¿Cuál es protocolo, clasificación/revisión ética, variables, outcome primario, cohortes, plan de análisis y criterios de inclusión/exclusión?
3. ¿Qué consentimiento corresponde a servicio, estudio, datos sensibles, información infantil, contacto profesional y cada canal? ¿Se requiere asentimiento/representación según datos y procedimiento?
4. ¿Qué base jurídica, avisos, derechos, plazos de respuesta, conservación, supresión/anonimización y archivo son aplicables? ¿Cómo se manejan retiro y retención científica/legal?
5. ¿Se permite enviar preguntas, historial, fragmentos o identificadores a Groq/Google? ¿Qué contratos, ubicación, subencargados, retención/entrenamiento y transferencias se autorizan?
6. ¿Qué variables se exportan y quién custodiará la tabla de enlace? ¿Qué reglas de supresión de celdas y reidentificación acepta comité?
7. ¿Se debe conservar cualquier contenido conversacional para investigación? Si sí, finalidad, minimización, cifrado, acceso, duración y consentimiento.
8. ¿Qué criterio detiene piloto ante un evento adverso, un falso negativo, indisponibilidad humana o una filtración?

### Tutor y equipo de ingeniería
1. ¿Cuál es aporte de tesis: arquitectura, evaluación de adherencia, RAG acotado, usabilidad, o viabilidad? ¿Cómo separar resultado de sistema de resultado de intervención?
2. ¿Qué alcance de implementación es viable en tiempo/equipo? ¿Es obligatorio probar en participantes o solo prototipo con datos sintéticos?
3. ¿Qué versión de Python/Flask/DB e infraestructura permite la institución? ¿Quién administra secrets, backups, alertas, actualizaciones y recuperación?
4. ¿Qué nivel/versión OWASP ASVS y criterio WCAG 2.2 AA se incluye en la definición de terminado?
5. ¿Qué métrica de adherencia tiene definición protocolizada y qué denominador representa participantes realmente elegibles?
6. ¿Cuál es el tamaño/calidad del benchmark RAG y quién adjudica conflictos entre fuentes? ¿Qué umbrales de liberación y plan de deriva se aprueban?
7. ¿Hay pruebas con lectores de pantalla/dispositivos y participantes? ¿Qué proceso ético y de consentimiento cubre la evaluación de usabilidad?
8. ¿Qué cargas, disponibilidad, RPO/RTO, gasto máximo de IA y plazo de respuesta humana se pueden financiar y operar?
