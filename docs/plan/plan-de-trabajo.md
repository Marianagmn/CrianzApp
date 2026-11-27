# Plan de trabajo — CrianzApp (trabajo de grado)

- **Versión:** 1.0 · 2026-11-27
- **Equipo:** Mariana Alejandra Gordillo Meneses · Julián David Cristancho Niño
- **Insumos:** [requisitos de la investigadora](../requisitos/01-requisitos-cliente.md), [supuestos](../requisitos/02-supuestos-y-pendientes.md), [arquitectura](../arquitectura/00-diseno-base-v1.md), [migración del chatbot](migracion-chatbot.md)

## 1. Cómo leer este plan

Son **16 semanas de trabajo** más una pausa de fin de año. Cada semana tiene un objetivo, lo que hace cada uno, lo que hacen entre los dos, lo que uno necesita del otro, las pruebas y la documentación que se entregan, y un criterio de «listo».

Las fechas suponen que se empieza el lunes 30 de noviembre de 2026. Si la universidad fija otra fecha de entrega, se mueven las semanas en bloque; el orden no cambia porque cada fase depende de la anterior.

## 2. Roles

Cada uno es **dueño** de un área: decide en ella, la documenta y aprueba los PR que la tocan. Todo lo demás se trabaja en partes iguales.

| | Mariana — IA y visual computing | Julián — Ciberseguridad |
|---|---|---|
| Dueña/o de | Asistente (RAG, agente, tools), evaluación de IA, clasificadores, simulador de role-play, visualización de datos, sistema de diseño | Identidad y acceso, consentimiento y privacidad, auditoría, seguridad del asistente (OWASP LLM Top 10), moderación, CI de seguridad, backups, pentests |
| Lo que muestra en la tesis | RAG con citas y abstención; agente con tool calling; clasificación por principio TBRI; sentimiento; alt-text con visión; role-play acotado; visualización del dashboard y de embeddings; evaluación con Ragas y golden set | Modelo de amenazas STRIDE + LLM Top 10; RBAC/ABAC con pruebas negativas (BOLA/IDOR); OWASP ASVS nivel 2; seudonimización y supresión de celdas pequeñas; enlaces firmados; SAST/DAST/secretos en CI; red-team del asistente; restauración probada |
| Revisa obligatoriamente | Todo PR que toque `assistant/`, `knowledge/`, `llm/`, prompts o el dashboard | Todo PR que toque `identity/`, `privacy/`, `audit/`, `security/`, autorización, cookies, subidas o la CI |

### Reglas para que la carga sea equitativa

1. **Las funcionalidades compartidas se parten por capa, no por quién tiene tiempo.** Regla por defecto: Mariana hace modelo de dominio, UI e inteligencia; Julián hace autorización, validación, API y endurecimiento. Así cada uno toca su especialidad dentro de cada funcionalidad.
2. **El liderazgo de las funcionalidades compartidas se alterna** (columna *Lidera* en §4): quien lidera define el issue y el criterio de aceptación, y el otro implementa su capa.
3. **Ningún PR se fusiona sin revisión del otro.** Además de calidad, así los dos entienden todo el sistema para defenderlo en la sustentación.
4. **Cada viernes** se revisa el tablero: si uno acumula más de dos días de retraso, el otro toma una tarea compartida de esa semana.
5. **Balance esperado:** [§5](#5-balance-de-carga) muestra días-persona por fase; se recalcula en cada retrospectiva.

## 3. Calendario

| Semana | Fechas | Fase | Hito con la investigadora |
|---|---|---|---|
| S1 | 30 nov – 4 dic | 0 · Fundaciones | Envío de supuestos SUP-01..19 |
| S2 | 7 – 11 dic (8 festivo) | 1 · Núcleo seguro | |
| S3 | 14 – 18 dic | 1 · Núcleo seguro | **Demo 1 + acta** |
| — | 21 dic – 8 ene | Pausa | Solo lecturas y marco teórico (opcional) |
| S4 | 12 – 15 ene (11 festivo) | 1 · Núcleo seguro | |
| S5 | 18 – 22 ene | 2 · Programa digital | Recibir 3 micro-módulos reales |
| S6 | 25 – 29 ene | 2 · Programa digital | |
| S7 | 1 – 5 feb | 2 · Programa digital | **Demo 2 + acta** |
| S8 | 8 – 12 feb | 3 · Monitoreo y escalamiento | |
| S9 | 15 – 19 feb | 3 · Monitoreo y escalamiento | Firma del protocolo de escalamiento |
| S10 | 22 – 26 feb | 3 · Monitoreo y escalamiento | **Demo 3 + acta** |
| S11 | 1 – 5 mar | 4 · Práctica y comunidad | Revisión de escenarios del role-play |
| S12 | 8 – 12 mar | 4 · Práctica y comunidad | |
| S13 | 15 – 19 mar | 4 · Práctica y comunidad | **Demo 4 + acta** |
| S14 | 23 – 24 mar (22, 25 y 26 festivos) | 5 · Endurecimiento | |
| S15 | 29 mar – 2 abr | 5 · Validación | Prueba de usabilidad |
| S16 | 5 – 9 abr | 5 · Cierre | **Entrega v1.0** |

S14 tiene solo dos días hábiles (San José y Semana Santa): se planificó la mitad de trabajo.

## 4. Semana a semana

### Fase 0 — Fundaciones

#### S1 · 30 nov – 4 dic · Repositorio profesional y línea base

**Objetivo:** que cualquiera pueda clonar, correr la CI y entender el proyecto; congelar el chatbot del Avance 3 como punto de partida medido.

| | Mariana | Julián |
|---|---|---|
| Propio | Etiquetar `avance-3-final` en el repo `AsistenteCrianza` e inventariar qué se migra ([migración](migracion-chatbot.md) M0). Copiar el golden set (15 preguntas + 7 casos de comportamiento) y los resultados Ragas como línea base. Sistema de diseño v0: tokens de color, tipografía y espaciado, con contraste AA verificado. Wireframes de onboarding, biblioteca, chat, check-in y dashboard. | Protección de `main` (PR obligatorio, revisión, CI verde). `pre-commit` con gitleaks y ruff. CI en GitHub Actions: ruff, pytest, bandit, pip-audit, gitleaks sobre todo el historial. `CODEOWNERS` con las rutas de §2. **Rotar las claves de Groq, Google y Tavily**, que quedaron en texto plano en chats y transcripciones del Avance 3. Modelo de amenazas v1 (STRIDE de la arquitectura objetivo + OWASP Top 10 for LLM Applications del asistente). |
| Compartido (lidera Mariana) | Crear el tablero en GitHub Projects con las etiquetas de [06-como-empezar.md](../arquitectura/06-como-empezar.md) §5 y un issue por RC. Mariana carga RC-01..12; Julián carga RC-13..23 y los de seguridad. Aceptar ADR-007 y ADR-008. Enviar a la investigadora la lista de supuestos con fecha límite. | |

- **Necesitamos del otro:** Julián necesita de Mariana la lista de servicios y secretos del chatbot (Groq, Gemini, Tavily) para el inventario y la rotación. Mariana necesita de Julián la CI lista antes de traer tests del chatbot (S3).
- **Pruebas:** la CI corre y pasa en un PR de prueba; gitleaks no encuentra secretos en el historial.
- **Documentación:** README, CONTRIBUTING, SECURITY, ADR-007/008, `docs/seguridad/modelo-amenazas.md` v1, `docs/ia/linea-base-avance3.md`.
- **Listo cuando:** el otro clona el repo en una máquina limpia, corre la CI y ve la línea base.

### Fase 1 — Núcleo seguro

#### S2 · 7 – 11 dic · Esqueleto de la aplicación

**Objetivo:** app Flask con capas, configuración por entorno y base de datos, sin funcionalidad de negocio todavía.

| | Mariana | Julián |
|---|---|---|
| Propio | Plantillas base en Jinja con el sistema de diseño (layout, navegación, mensajes, estados vacío/error/cargando). Páginas públicas: inicio, marco TBRI con lugar para el video (RC-01) y transparencia de la IA (RC-04). Base de accesibilidad: *skip link*, foco visible, landmarks. | Configuración con validación al arranque: falla cerrada si falta `SECRET_KEY` o la URL de la BD. `docker-compose` (app + PostgreSQL) con usuario no root e imagen slim. Alembic inicializado. Cabeceras de seguridad y CSP con Flask-Talisman. |
| Compartido (lidera Julián) | `create_app()` y estructura `presentation / application / domain / infrastructure` de [03-arquitectura-y-api.md](../arquitectura/03-arquitectura-y-api.md) §3. Julián: composición de dependencias y entornos. Mariana: blueprints y registro de plantillas. | |

- **Necesitamos del otro:** Julián necesita el layout base para meter el token CSRF y el nonce de CSP; Mariana necesita el módulo de configuración para las rutas públicas.
- **Pruebas:** smoke test de `create_app` en dev/test; test de que la app no arranca sin `SECRET_KEY`; Playwright + axe-core sin violaciones serias en las páginas públicas; test de cabeceras (CSP, HSTS, X-Content-Type-Options).
- **Documentación:** árbol real en `03-arquitectura-y-api.md`; ADR-009 (sesión en servidor vs. JWT) por Julián.
- **Listo cuando:** `docker compose up` levanta la app con PostgreSQL y las páginas públicas pasan accesibilidad.

#### S3 · 14 – 18 dic · Cuentas y consentimiento · **Demo 1**

**Objetivo:** un cuidador sintético puede registrarse, entrar y dar consentimientos separados.

| | Mariana | Julián |
|---|---|---|
| Propio | Pantallas de registro, login y recuperación, accesibles y con errores claros. **Migración M1:** crear `AssistantPort` y mover `knowledge_base`, `tools`, `safety` y el agente del Avance 3 a `infrastructure/knowledge` y `application/assistant`, con sus tests pasando en el repo nuevo. | Registro, login, logout y recuperación (RF-01): argon2id, sesión con rotación al autenticar, cookies `Secure`/`HttpOnly`/`SameSite=Lax`, CSRF, rate limit y bloqueo progresivo en login, mensajes neutros que no revelan si un correo existe, token de recuperación de un solo uso con expiración. |
| Compartido (lidera Julián) | Consentimiento (RF-03, RC-03). Julián: `ConsentRecord` inmutable por finalidad y versión, servicio de aceptar/retirar, bloqueo de funciones sin consentimiento. Mariana: aviso por capas y elecciones separadas (servicio, investigación, contacto, WhatsApp), textos marcados BORRADOR (SUP-17). | |

- **Necesitamos del otro:** Mariana necesita los endpoints de auth para conectar las pantallas; Julián necesita las pantallas para las pruebas E2E de sesión.
- **Pruebas:** T-AUTH (credencial válida entra, inválida/expirada no, la recuperación no revela el correo, el rate limit corta tras N intentos); T-CONSENT (sin consentimiento de investigación no se habilita nada del estudio; retirar bloquea); tests del chatbot migrado en verde.
- **Documentación:** `docs/seguridad/autenticacion.md`; M1 marcado en el plan de migración.
- **Demo 1 + acta:** landing, registro y consentimiento con textos borrador. Pedirle a la investigadora textos de consentimiento, el video y 3 micro-módulos reales.
- **Listo cuando:** el flujo registro → consentimiento → inicio funciona de punta a punta con datos sintéticos.

#### Pausa · 21 dic – 8 ene

Sin entregables. Opcional: Mariana mapea el corpus TBRI a futuros micro-módulos; Julián lee la checklist de OWASP ASVS nivel 2. Los dos pueden avanzar el marco teórico de la tesis.

#### S4 · 12 – 15 ene · Roles, onboarding y auditoría

**Objetivo:** cada usuario solo ve lo que su rol y su cohorte le permiten, y queda rastro de las acciones sensibles.

| | Mariana | Julián |
|---|---|---|
| Propio | Onboarding y caracterización (RC-02, RF-04): formulario multipaso accesible, con preguntas definidas en YAML versionado (cambiar SUP-05 no requiere tocar código). **Generador de datos sintéticos** (cuidadores, cohortes, actividad) que usará todo el proyecto, sobre todo el dashboard. | Roles y alcances (RF-02): cuidador, facilitador, investigador, admin técnico, editor (SUP-16). Autorización en el backend con un decorador `@requiere(permiso, alcance)` y denegación por defecto. `AuditEvent` de solo inserción y sin contenido sensible. Separación de tablas: identidad (correo) por un lado y `participant_id` seudónimo por otro. |
| Compartido (lidera Mariana) | Cohortes y sesiones del programa (RF-05). Mariana: modelos `Cohort`/`ProgramSession` y seeds. Julián: un participante solo ve su cohorte; acceso a otra se deniega y se audita. | |

- **Necesitamos del otro:** Mariana necesita los roles para restringir el onboarding; Julián necesita los datos sintéticos para las pruebas de cuentas A/B.
- **Pruebas:** T-AUTHZ con la matriz completa de [04](../arquitectura/04-datos-privacidad-ia-seguridad.md) §2 (cuenta A no lee nada de B; cambiar `participant_id` en la URL no da acceso); T-PRIV (el onboarding no guarda campos fuera del YAML aprobado; el admin técnico no ve respuestas individuales).
- **Documentación:** `docs/seguridad/matriz-permisos.md`; `docs/datos/diccionario.md` v1.
- **Listo cuando:** la suite negativa de permisos pasa al 100 % para todas las rutas existentes.

### Fase 2 — Programa digital

#### S5 · 18 – 22 ene · Biblioteca de micro-módulos

**Objetivo:** el cuidador navega micro-módulos por principio TBRI y solo ve contenido publicado.

| | Mariana | Julián |
|---|---|---|
| Propio | **Alt-text de infografías con visión** (reutiliza el taller multimodal): el modelo propone una descripción y el editor la aprueba o corrige; nunca se publica sola (WCAG 1.1.1). Se mide cuántas propuestas acepta el editor sin cambios. | Subida de archivos segura: tipo real por contenido (no por extensión), límite de tamaño, nombre aleatorio, fuera de la raíz web. Sanitización del contenido enriquecido (nh3). CSP que solo permita los dominios de video aprobados. |
| Compartido (lidera Mariana) | Biblioteca (RC-05, RC-06, RF-06/07). Mariana: `ContentItem`/`ContentVersion`, vistas por principio (Empoderamiento, Conexión, Corrección), los formatos (texto estructurado, infografía, video, resumen, descargable) y marcar como completado. Julián: flujo editorial con separación autor → revisor → publicador (RF-17), versión inmutable y retiro. | |

- **Necesitamos del otro:** Mariana necesita los permisos de editor y revisor; Julián necesita el modelo de contenido para el flujo editorial.
- **Pruebas:** T-CONTENT (un borrador da 404 al cuidador; un autor no puede aprobar lo suyo); payloads XSS en el contenido; subida de un archivo disfrazado rechazada; test del pipeline de alt-text con las imágenes del taller.
- **Documentación:** `docs/ia/alt-text-vision.md` (modelo, prompt, revisión humana, tasa de aceptación); `docs/contenido/guia-editorial.md`.
- **Investigadora:** recibir 3 micro-módulos reales; si no llegan, se publican 3 de ejemplo derivados del corpus.
- **Listo cuando:** se publica un micro-módulo por principio con el flujo editorial completo.

#### S6 · 25 – 29 ene · Chat integrado (migración M2 y M3)

**Objetivo:** el agente del Avance 3 vive dentro de la app autenticada, con controles de seguridad y sin guardar conversaciones.

| | Mariana | Julián |
|---|---|---|
| Propio | Chat en la app (RC-07): historial en el servidor con expiración (ya no lo envía el navegador). **Corregir la regresión del Avance 3**: el agente sacó faithfulness 0,07 frente a 0,54 del pipeline fijo; volver a citas numeradas `[Fuente N]` con tabla de fuentes y volver a medir. Gobernanza del corpus: versión, checksum y estado de aprobación por documento; comando `flask corpus rebuild`. | Seguridad del asistente: rate limit y cuota por cuenta, longitud máxima, redacción de datos personales antes de enviar al LLM (teléfonos, correos, documentos), lista blanca de salida (solo Groq y Tavily), timeouts y *circuit breaker*. Test que captura logs y falla si aparece el texto de una pregunta. |
| Compartido (lidera Mariana) | Evento de uso del asistente con metadatos mínimos (`AssistantTurn`: modo, tools usadas, principio, abstención, escalado; sin texto). Mariana define los campos que necesita el dashboard (RC-22); Julián confirma que cumplen ADR-005. **Suite de prompt injection v1**: Julián escribe los ataques, Mariana corrige el agente. | |

- **Necesitamos del otro:** Julián necesita el chat migrado para atacarlo; Mariana necesita la redacción de datos y el rate limit antes de abrir el chat a cuentas.
- **Pruebas:** golden set + 7 casos de comportamiento en CI (LLM simulado); Ragas real del agente corregido; test «ni la pregunta ni la respuesta aparecen en BD ni en logs»; suite de prompt injection sin fallas críticas.
- **Documentación:** ADR-010 (historial en servidor con TTL); M2 y M3 en el plan de migración; `docs/ia/evaluacion.md` con la comparación antes/después de la corrección de citas.
- **Listo cuando:** un cuidador con sesión conversa con el agente, ve fuentes, y no queda texto de la conversación en ningún almacenamiento.

#### S7 · 1 – 5 feb · Check-ins, recomendación y adherencia · **Demo 2**

**Objetivo:** el sistema registra la adherencia activa y pasiva que pidió la investigadora, y el chat lleva a la biblioteca.

| | Mariana | Julián |
|---|---|---|
| Propio | Enrutamiento interno y recomendación (RC-08, RC-09): tool `recomendar_modulo` que busca por embeddings en el catálogo publicado; cada cita enlaza a `/biblioteca/<modulo>`. **Clasificador de principio TBRI** por turno (Empoderamiento/Conexión/Corrección) para RC-22.1: etiquetar 60 preguntas con la investigadora o con criterio documentado, comparar un clasificador zero-shot con LLM contra embeddings + centroides, reportar macro-F1 y matriz de confusión. | Telemetría de adherencia pasiva (RC-19.2): inicios de sesión, días activos, tiempo en módulo; los eventos se generan en el servidor y el cliente no puede inventarlos. Endpoint de check-in idempotente (`Idempotency-Key`) con validación de ventana. |
| Compartido (lidera Julián) | Check-ins (RC-10..12, RF-11). Mariana: UI de 1 clic para estrés/ánimo, micro-consulta al terminar un módulo, registro de práctica en el hogar. Julián: API, reglas de frecuencia (SUP-02) y validación. | |

- **Necesitamos del otro:** Mariana necesita los eventos de actividad para recomendar según lo que el cuidador ya vio; Julián necesita la UI de check-in para la prueba E2E de idempotencia.
- **Pruebas:** T-ACTIVITY (enviar dos veces el mismo check-in = un evento); evaluación del clasificador con meta macro-F1 ≥ 0,80 (ajustable); los eventos no contienen datos personales; E2E: registro → onboarding → módulo → check-in → chat.
- **Documentación:** `docs/datos/metricas-adherencia.md` con numerador, denominador y ventana de cada variable RC-19/20; `docs/ia/clasificador-principios.md`.
- **Demo 2 + acta:** recorrido completo del cuidador con datos sintéticos. Confirmar SUP-02, SUP-03 y SUP-06.
- **Listo cuando:** las variables RC-19.1 a 19.3 se pueden calcular con los eventos guardados.

### Fase 3 — Monitoreo y escalamiento

#### S8 · 8 – 12 feb · Dashboard del equipo facilitador

**Objetivo:** el facilitador ve la adherencia de su cohorte y de sus participantes asignados; el admin técnico solo ve agregados.

| | Mariana | Julián |
|---|---|---|
| Propio | Visualizaciones (visual computing): serie de adherencia semanal, *heatmap* de actividad por participante y día, embudo de completitud de la ruta, distribución de estrés. Todas con tabla alternativa y sin depender solo del color. | Agregados con autorización por cohorte; **supresión de celdas pequeñas** (grupos menores a k se ocultan, k=5 por defecto); vista individual solo para facilitadores asignados (RF-15), con auditoría de cada acceso; export CSV protegido contra CSV injection. |
| Compartido (lidera Mariana) | Dashboard (RC-14, RC-19, RC-20, RF-14). Mariana: consultas de las métricas de `metricas-adherencia.md` y la UI. Julián: registro de asistencia a sesiones sincrónicas por el facilitador (SUP-07) y su autorización. | |

- **Necesitamos del otro:** Mariana necesita la supresión y la autorización antes de mostrar cualquier dato; Julián necesita las consultas para revisar que no filtren identidad.
- **Pruebas:** T-AGG (el admin técnico no obtiene filas individuales por ningún filtro); test de supresión con una cohorte de 3 personas; test de CSV injection; regresión visual de las gráficas con Playwright.
- **Documentación:** `docs/dashboard/variables.md`: cada variable de la investigadora con su consulta, su gráfica y su permiso.
- **Listo cuando:** las seis variables de §3.1 de los requisitos se ven en el dashboard con datos sintéticos.

#### S9 · 15 – 19 feb · Escalamiento humano

**Objetivo:** las situaciones de riesgo, estrés alto e inactividad llegan al facilitador correcto, sin diagnosticar.

| | Mariana | Julián |
|---|---|---|
| Propio | El agente crea una señal de riesgo con solo la categoría, nunca el texto. Métricas RC-22.2 (tasa de escalamiento) y RC-22.3 (autonomía de la IA). **Mejorar el detector de riesgo:** complementar las expresiones regulares de `safety.py` con un clasificador evaluado, priorizando *recall* (no se escapan casos), con matriz de confusión y casos difíciles (negaciones, errores de tipeo, contexto). Solo prioriza la revisión humana. | `SafetySignal` y cola del facilitador (RC-15, RF-10): disparadores (riesgo en el chat, estrés según SUP-03, inactividad según SUP-04), asignación, estados, tiempo de atención visible, auditoría. Tarea programada de inactividad, idempotente. Lo que ve el cuidador dentro y fuera de horario. |
| Compartido (lidera Julián) | Protocolo de escalamiento en borrador (SUP-18) para la firma de la investigadora: quién atiende, horarios, mensajes, líneas 141, 123 y 106 verificadas. | |

- **Necesitamos del otro:** Julián necesita el evento de riesgo del agente; Mariana necesita la cola para probar el flujo completo.
- **Pruebas:** T-SAFETY con todas las ramas («no quiero hacerle daño» no dispara; «quiero hacerme daño» sí); una señal no es visible para un facilitador no asignado; falsos positivos y negativos documentados.
- **Documentación:** `docs/seguridad/protocolo-escalamiento.md` (BORRADOR); `docs/ia/detector-riesgo.md`.
- **Investigadora:** firma del protocolo. Sin firma, el escalamiento solo funciona con datos sintéticos.
- **Listo cuando:** los tres disparadores generan una señal que solo ve el facilitador asignado.

#### S10 · 22 – 26 feb · Encuestas de experiencia y pentest 1 · **Demo 3**

**Objetivo:** se aplican las escalas de aceptabilidad y usabilidad, y se audita la seguridad de lo construido.

| | Mariana | Julián |
|---|---|---|
| Propio | **Sentimiento del micro-feedback** (RC-21.5) con un modelo en español; evaluado sobre una muestra etiquetada; en el dashboard solo agregados. | **Pentest interno 1:** OWASP ZAP baseline en CI y revisión manual de autenticación, sesión y control de acceso contra ASVS nivel 2. Informe con hallazgos y su corrección. |
| Compartido (lidera Mariana) | Motor de instrumentos (RC-21, RC-23; reutiliza el de onboarding). Mariana: UI y cálculo de puntajes de SUS, usabilidad del chatbot, UES-SF, WAI-SR adaptado y NPS. Julián: cuándo aparece cada escala (pre/post/seguimiento), consentimiento por finalidad y **export seudonimizado** para la investigadora (RF-16) con bitácora y expiración. | |

- **Necesitamos del otro:** Mariana necesita la programación de las escalas para mostrarlas; Julián necesita los puntajes para el export.
- **Pruebas:** puntaje SUS contra un ejemplo publicado; el export no trae correo ni identidad; ZAP sin hallazgos altos.
- **Documentación:** `docs/instrumentos/` (fuente, licencia y forma de puntuar cada escala); `docs/seguridad/pentest-1.md`.
- **Demo 3 + acta:** dashboard, escalamiento y encuestas. Confirmar SUP-01 y SUP-10.
- **Listo cuando:** la investigadora puede descargar un export seudonimizado de prueba con adherencia y escalas.

### Fase 4 — Práctica y comunidad (detrás de *feature flags*, ADR-008)

#### S11 · 1 – 5 mar · Simulador de role-play

**Objetivo:** el cuidador practica una situación difícil con un niño simulado y recibe retroalimentación basada en TBRI.

| | Mariana | Julián |
|---|---|---|
| Propio | Simulador (RC-16), **lidera**: escenarios curados en YAML (SUP-12), máquina de estados (escenario → respuesta del padre → reacción del niño → feedback con rúbrica TBRI), modo «guía al personaje», aviso permanente de que es ficción. Prompts versionados; evaluación del feedback contra la rúbrica en escenarios dorados. | **Red-team del simulador:** intentos de sacar al personaje del escenario, de generar contenido dañino o de extraer instrucciones; límites de turnos y tokens; filtro de salida; `safety` en cada respuesta del padre, y si revela un riesgo real se sale de la simulación al flujo de seguridad. |

- **Necesitamos del otro:** Julián necesita el simulador en staging para atacarlo; Mariana necesita los resultados del red-team para ajustar los prompts.
- **Pruebas:** escenarios dorados con la rúbrica esperada; suite de red-team del simulador; test «un riesgo real interrumpe la simulación».
- **Documentación:** `docs/ia/simulador.md`; ADR-011 (límites del role-play).
- **Listo cuando:** los 6 escenarios pasan la evaluación y el red-team no encuentra fallas críticas.

#### S12 · 8 – 12 mar · Gimnasio relacional

**Objetivo:** juegos breves de repaso y retos diarios que sostienen la adherencia.

| | Mariana | Julián |
|---|---|---|
| Compartido (lidera Julián) | Mariana: desafío «necesidades vs. conductas» y verdadero/falso con un banco de ítems versionado; diseño de las insignias en SVG coherentes con el sistema de diseño; vista de progreso. Julián: motor de reglas de insignias y retos diarios en el servidor (el cliente no puede marcarse completado ni otorgarse insignias), sin otorgar dos veces, sin ranking entre participantes; progreso (RF-12). | |

- **Necesitamos del otro:** Mariana necesita los eventos de las reglas para mostrar el progreso; Julián necesita el banco de ítems para validar respuestas en el servidor.
- **Pruebas:** reglas de insignias (unitarias); una insignia no se otorga dos veces; una petición falsificada no completa un reto.
- **Documentación:** `docs/funcionalidades/gamificacion.md` con el criterio de cada insignia (SUP-14).
- **Listo cuando:** un cuidador sintético completa un reto diario y gana una insignia de forma verificable.

#### S13 · 15 – 19 mar · Muro de conexión y boosters por WhatsApp · **Demo 4**

**Objetivo:** apoyo entre pares moderado y recordatorios que devuelven al cuidador al módulo exacto.

| | Mariana | Julián |
|---|---|---|
| Compartido muro (lidera Julián) | Mariana: feed de historias y victorias, reacciones, preguntas semanales de los facilitadores, avatares generados para el modo anónimo. Julián: pre-moderación (SUP-11), reportes y bloqueo, seudónimo que no se puede enlazar con la cuenta desde fuera, rate limit, sanitización. | |
| Compartido WhatsApp (lidera Mariana) | Mariana: página de aterrizaje del booster y medición de clics para RC-19.4 en el dashboard. Julián: enlaces firmados con HMAC y expiración, sin identidad en la URL; plantillas sin datos sensibles; opt-in por canal (SUP-09). | |

- **Necesitamos del otro:** cada uno necesita la capa del otro en las dos funcionalidades; se integran el miércoles.
- **Pruebas:** un post no aprobado es invisible; XSS en posts y comentarios; un enlace manipulado o vencido se rechaza; el rate limit frena el spam.
- **Documentación:** `docs/funcionalidades/muro.md` (reglas de moderación para firma); `docs/seguridad/enlaces-firmados.md`.
- **Demo 4 + acta:** simulador, gimnasio, muro y booster. Pedir firma de SUP-11 y SUP-12.
- **Listo cuando:** un booster lleva a un módulo y el clic aparece en el dashboard; un post pasa por moderación.

### Fase 5 — Endurecimiento, validación y cierre

#### S14 · 23 – 24 mar · Endurecimiento (semana corta)

| | Mariana | Julián |
|---|---|---|
| Propio | Evaluación final de IA sobre la versión integrada frente a la línea base del Avance 3: Ragas, golden set, casos de comportamiento, clasificador de principios, detector de riesgo y sentimiento. **Visualización de embeddings** del corpus y de las consultas por principio (UMAP) para la tesis. | **Pentest interno 2:** OWASP Top 10, OWASP LLM Top 10 y checklist ASVS nivel 2 con evidencia. SBOM. Backup cifrado y **simulacro de restauración** con RPO/RTO medidos. |

- **Documentación:** `docs/ia/evaluacion-final.md`; `docs/seguridad/informe-final.md`; acta de restauración.

#### S15 · 29 mar – 2 abr · Staging y prueba de usabilidad

| | Mariana | Julián |
|---|---|---|
| Compartido (lidera Mariana) | Mariana: guion de la prueba de usabilidad con 5–8 personas (compañeros, datos sintéticos, consentimiento de la prueba), aplicación del SUS y análisis. Julián: staging en Railway con PostgreSQL administrado, monitoreo, alertas y plan de respuesta a incidentes. Los dos corrigen los hallazgos por partes iguales. | |

- **Documentación:** `docs/validacion/usabilidad.md`; `docs/operacion/runbook.md`.

#### S16 · 5 – 9 abr · Cierre v1.0

| | Mariana | Julián |
|---|---|---|
| Compartido | Tag `v1.0.0` y CHANGELOG. Manual del cuidador y del facilitador. Capítulos de la tesis: Mariana escribe IA y visualización; Julián escribe seguridad y privacidad; los dos escriben arquitectura, resultados y conclusiones. Video demo y presentación. Retrospectiva. | |

## 5. Balance de carga

Días-persona estimados por fase (se recalcula en cada retrospectiva). La diferencia aceptable es ±10 %.

| Fase | Mariana | Julián | Total |
|---|---:|---:|---:|
| 0 · Fundaciones (S1) | 5 | 5 | 10 |
| 1 · Núcleo seguro (S2–S4) | 13 | 13 | 26 |
| 2 · Programa digital (S5–S7) | 15 | 15 | 30 |
| 3 · Monitoreo y escalamiento (S8–S10) | 15 | 15 | 30 |
| 4 · Práctica y comunidad (S11–S13) | 15 | 15 | 30 |
| 5 · Cierre (S14–S16) | 12 | 12 | 24 |
| **Total** | **75** | **75** | **150** |

Las funcionalidades compartidas que lidera cada uno quedan así: Mariana lidera 8 (tablero, biblioteca, chat, cohortes, dashboard, instrumentos, WhatsApp, usabilidad); Julián lidera 6 (esqueleto, consentimiento, check-ins, escalamiento, gimnasio, muro). Mariana lidera más porque el producto es, en su mayoría, IA y UI; Julián compensa con los dos pentests, la CI de seguridad, el red-team y la restauración, que no aparecen como funcionalidades.

## 6. Trabajo con la investigadora

- **Cada dos o tres semanas, una demo de 30 minutos** con acta (S3, S7, S10, S13). Se muestran decisiones tomadas, no preguntas abiertas: «hicimos X; ¿lo dejamos así o lo cambiamos?».
- Los supuestos **tipo E** se dan por aceptados si no los objeta en 5 días hábiles. Los **tipo A** (consentimiento, escalamiento, instrumentos, muro, role-play) nunca se dan por aceptados sin firma ([supuestos](../requisitos/02-supuestos-y-pendientes.md)).
- Lo que pida después de una demo entra como issue nuevo con la etiqueta `cambio-cliente` y se prioriza en la siguiente retrospectiva; no interrumpe la semana en curso.
- **Solo datos sintéticos** hasta tener el aval de ética institucional (SUP-19).

## 7. Riesgos del plan

| Riesgo | Señal temprana | Respuesta |
|---|---|---|
| La investigadora no entrega contenido ni firmas | S5 sin micro-módulos; S9 sin protocolo | Contenido de ejemplo del corpus y funciones con datos sintéticos; queda documentado en el acta |
| Cuota de Groq agotada en evaluaciones | En el Avance 3 se agotó el límite diario por organización (200 000 tokens) | LLM simulado en CI; evaluaciones reales espaciadas y programadas; presupuesto o segunda cuenta para S14 |
| Licencias de escalas | El instrumento elegido es comercial | El motor de instrumentos funciona con cualquier escala; se documenta la licencia antes de cargarla |
| La fase 4 no cabe | A S10 el núcleo no está terminado | Las banderas se quedan apagadas y se entrega sin la fase 4 (ADR-008) |
| Carga desigual | Una persona con más de 2 días de retraso | Regla 4 de §2 |
| Seguridad tratada al final | Hallazgos altos en el pentest 1 | El pentest 1 está en S10 justamente para corregir antes de la fase 4 |

## 8. Evidencia para la tesis por fase

| Fase | Evidencia |
|---|---|
| 0 | Línea base del Avance 3, modelo de amenazas v1, ADR-007/008 |
| 1 | Matriz de permisos y su suite negativa, diseño del consentimiento |
| 2 | Comparación de citas antes/después, evaluación del clasificador de principios, tasa de aceptación del alt-text |
| 3 | Variables del dashboard trazadas a la investigadora, evaluación del detector de riesgo y del sentimiento, informe del pentest 1 |
| 4 | Evaluación del simulador y su red-team, pruebas de los controles del muro y de los enlaces firmados |
| 5 | Evaluación final de IA, informe de seguridad final, acta de restauración, informe de usabilidad |
