# 01. Requisitos de la investigadora (fuente: «Dashboard TBRI y contenido de la web»)

- **Fuente:** documento `.docx` enviado por la investigadora como respuesta a la propuesta del equipo (recibido 2026-11). Este archivo lo transcribe y lo traza; el `.docx` original no se versiona aquí.
- **Regla de lectura:** lo que la investigadora escribió está en la columna *Lo que pidió* y no se reinterpreta. Lo que **no** dijo y hace falta para construir se resuelve en [02-supuestos-y-pendientes.md](02-supuestos-y-pendientes.md) con un valor por defecto, y se corrige después si ella lo pide.
- **IDs:** `RC-xx` = requisito de cliente. Cada uno apunta a los `RF-xx` de [../arquitectura/02-especificacion-funcional.md](../arquitectura/02-especificacion-funcional.md) y a la semana del [plan de trabajo](../plan/plan-de-trabajo.md) donde se construye.

## 1. Objetivos del estudio (contexto para el producto)

**Objetivo general:** evaluar el efecto de un programa de parentalidad basado en TBRI en modalidad virtual sobre las prácticas disciplinarias, la relación niño-cuidador y el estrés parental en cuidadores de niños en primera infancia, y analizar el papel de los apoyos digitales con IA en la adherencia a la intervención.

| # | Objetivo específico | Qué le exige a la plataforma |
|---|---|---|
| OE-1 | Efecto sobre prácticas disciplinarias (menos violentas, más no violentas) | Aplicar/exportar el instrumento de prácticas en pre, post y seguimiento |
| OE-2 | Efecto sobre estrés parental | Instrumento de estrés pre/post/seguimiento + check-in de estrés 1 clic |
| OE-3 | Cambios en la relación niño-cuidador | Instrumento de relación pre/post/seguimiento |
| OE-4 | Medir adherencia al programa | Variables de adherencia digital y sincrónica (RC-19, RC-20) |
| OE-5 | ¿La web con chat se asocia a mayor adherencia? | Registrar uso del chat por participante (seudónimo) para cruzarlo con adherencia |
| OE-6 | Aceptabilidad y usabilidad del apoyo digital | Escalas SUS, usabilidad del chatbot, UES-SF, WAI-SR, NPS, sentimiento (RC-21) |
| OE-7 | Asociación adherencia ↔ resultados | Export seudonimizado que una adherencia con outcomes (RC-23) |
| OE-8 | La IA como facilitadora de adherencia y resultados | Métricas del motor de IA (RC-22) |

> La plataforma **produce y exporta los datos**; el análisis estadístico (asociación, efecto) lo hace la investigadora. El software no infiere causalidad.

## 2. Contenidos de la web

| ID | Lo que pidió | RF | Semana | Notas del equipo |
|---|---|---|---|---|
| RC-01 | Información del servicio y marco TBRI, con **video** | RF-06 | S2 | Página pública. El video lo entrega ella (SUP-08). |
| RC-02 | Registro y caracterización inicial: datos sociodemográficos **mínimos** del cuidador y del niño/a | RF-01, RF-04 | S3–S4 | Campos por defecto en SUP-05. Nada que identifique al niño (sin nombre ni fecha exacta). |
| RC-03 | Consentimiento informado digital; aceptación de políticas de privacidad y datos sensibles | RF-03 | S3 | Texto final requiere su aval (SUP-17). Consentimientos separados por finalidad. |
| RC-04 | Transparencia técnica: la IA complementa, no sustituye, la facilitación humana | RF-08 | S2, S6 | Página + aviso fijo en el chat. |
| RC-05 | Biblioteca de micro-módulos de **10–15 min** organizados por los 3 principios: Empoderamiento, Conexión, Corrección | RF-06, RF-07 | S5 | Contenido real lo entrega ella; mientras tanto, 3 módulos de ejemplo derivados del corpus TBRI. |
| RC-06 | Formatos: **texto estructurado priorizado**, infografías, videos cortos, resúmenes ejecutivos, guías descargables o interactivas | RF-06 | S5 | Alt-text de infografías asistido por visión + revisión humana. |
| RC-07 | Chat integrado en lenguaje natural sobre técnicas y situaciones cotidianas | RF-08 | S6 | Migración del agente del Avance 3 ([plan de migración](../plan/migracion-chatbot.md)). |
| RC-08 | IA generativa **acotada** que reformula, adapta el lenguaje y **recomienda** lecturas/ejercicios de la biblioteca | RF-08 | S7 | Recomienda solo contenido publicado; nunca inventa módulos. |
| RC-09 | Citas y enrutamiento interno: las respuestas llevan al micro-módulo correspondiente | RF-08 | S7 | Cada cita enlaza a `/biblioteca/<modulo>`. |
| RC-10 | Micro-consultas periódicas (1–3 min) al iniciar sesión o tras completar un taller | RF-11 | S7 | Frecuencia por defecto: SUP-02. |
| RC-11 | Escala de 1 clic de estado de ánimo/estrés | RF-11 | S7 | Escala 1–5 por defecto (SUP-03). |
| RC-12 | Registro de práctica en el hogar (ejercicios de la semana) | RF-11, RF-12 | S7 | |
| RC-13 | WhatsApp: boosters, alertas y recordatorios con **enlaces que devuelven al módulo** en la web | RF-18, RF-24 | S13 | Enlaces firmados con medición de clic; modo de envío en SUP-09. Sustituye parcialmente ADR-006 (ver ADR-008). |
| RC-14 | Panel de monitoreo del equipo facilitador: trazabilidad de uso, completitud, check-ins e indicadores de adherencia **por participante** | RF-14, RF-15 | S8 | Vista individual solo para facilitadores asignados y auditada. «Tiempo real» → SUP-13. |
| RC-15 | Escalamiento humano: alertas automáticas por riesgo, estrés elevado o inactividad prolongada | RF-10 | S9 | Umbrales por defecto SUP-03/SUP-04. Protocolo final requiere su aval. |
| RC-16 | Simulador de role-play con IA: escenario → respuesta del padre → reacción simulada del niño → feedback con principios TBRI; modo «guía al personaje» | RF-23 | S11 | Escenarios curados, ficción explícita y salida inmediata al flujo de seguridad. ADR-008. |
| RC-17 | Gimnasio relacional (2–5 min): necesidades vs. conductas, verdadero/falso de regulación, retos diarios de conexión, **insignias** | RF-12 (+ nuevo RF-25) | S12 | Criterios de insignias por defecto en SUP-14. |
| RC-18 | Muro de conexión: historias y victorias, comentarios y reacciones, preguntas semanales de facilitadores, **anonimato opcional**, **moderación activa** | RF-22 | S13 | Pre-moderación por defecto (SUP-11). ADR-008. |

## 3. Variables del dashboard

### 3.1 Uso de la app con chat y adherencia (OE-4, OE-5)

| ID | Categoría | Variable | Definición operativa (de ella) | Unidad | Tipo de adherencia |
|---|---|---|---|---|---|
| RC-19.1 | Adherencia digital directa | Tasa de respuesta a check-ins | Check-ins respondidos ÷ enviados | % (0–100) | Activa |
| RC-19.2 | Adherencia digital directa | Frecuencia e inicios de sesión | Días activos por semana; inicios por mes | días/semana; inicios/mes | Pasiva |
| RC-19.3 | Adherencia digital directa | Completitud de micro-intervenciones | % de módulos, lecturas, videos o ejercicios completados | % de la ruta | Activa |
| RC-19.4 | Adherencia digital directa | Interacción con boosters | Clics/interacción con refuerzos y recordatorios (web o WhatsApp) | % de respuesta | Activa |
| RC-20.1 | Adherencia al programa TBRI | Asistencia a sesiones sincrónicas | Talleres virtuales grupales asistidos ÷ total | sesiones | Registro del facilitador (SUP-07) |
| RC-20.2 | Adherencia al programa TBRI | Retención longitudinal | Semanas continuas de participación activa hasta el postest | semanas | Derivada |

Definiciones que usa el sistema (ella las dio):

- **Adherencia digital pasiva:** datos de interacción recolectados automáticamente en segundo plano, sin acción deliberada del usuario.
- **Adherencia digital activa:** acciones voluntarias e intencionales del cuidador en la plataforma o con el asistente.

### 3.2 Aceptabilidad, usabilidad y alianza digital (OE-6)

| ID | Dimensión | Instrumento | Indicador | Punto de corte |
|---|---|---|---|---|
| RC-21.1 | Usabilidad de la web | System Usability Scale (SUS) | Puntuación global | *No lo dio* → SUP-10 |
| RC-21.2 | Usabilidad del chatbot | «Chatbot Usability Scale (ITA-BUS B / uMARS)» | Facilidad, claridad conversacional, calidad percibida | *No lo dio*; confirmar cuál instrumento exacto (SUP-10) |
| RC-21.3 | Involucramiento | User Engagement Scale Short-Form (UES-SF) | Atención, interés, involucramiento | *No lo dio* |
| RC-21.4 | Alianza terapéutica digital | WAI-SR adaptado | Vínculo, metas, tareas con el asistente | Promedio 1–5 |
| RC-21.5 | Aceptabilidad cualitativa | Análisis de sentimiento in-app | Texto libre del micro-feedback post-módulo | Positivo/Neutral/Negativo (%) |
| RC-21.6 | Satisfacción global | NPS / calificación | Probabilidad de recomendar | *No lo dio* (NPS estándar −100 a 100) |

### 3.3 La IA como facilitadora (OE-8)

| ID | Variable | Definición (de ella) | Cómo la obtiene el sistema sin guardar conversaciones (ADR-005) |
|---|---|---|---|
| RC-22.1 | Volumen conversacional por principio TBRI | Turnos y consultas categorizadas en Empoderamiento, Conexión y Corrección | Clasificador de principio por turno; se guarda solo la etiqueta, no el texto |
| RC-22.2 | Tasa de escalamiento humano | % de interacciones/check-ins que activan alertas por estrés o riesgo | Conteo de `SafetySignal` ÷ interacciones |
| RC-22.3 | Nivel de autonomía de la IA | % de dudas resueltas por la IA vs. derivadas al equipo humano | Turnos `respondido` vs. `abstención`/`derivado` |

### 3.4 Adherencia ↔ resultados (OE-7)

| ID | Lo que pidió | Estado |
|---|---|---|
| RC-23 | Relacionar adherencia digital con el cambio pre-post-seguimiento en prácticas disciplinarias, estrés parental y relación niño-cuidador | **No nombró los instrumentos** de los tres outcomes (SUP-01). La plataforma deja un motor de cuestionarios versionado y un export seudonimizado; el análisis lo hace ella. |

## 4. Diferencias con la planeación previa del equipo

La planeación de octubre ([00-diseno-base-v1.md](../arquitectura/00-diseno-base-v1.md) §3) dejaba fuera del MVP el foro, WhatsApp, el role-play y la gamificación. La investigadora los pidió explícitamente. Decisión ([ADR-008](../arquitectura/adr/ADR-008-alcance-ampliado-por-cliente.md)): **se construyen**, después del núcleo seguro, detrás de *feature flags* y con los controles de seguridad de cada semana. Si el tiempo no alcanza, el producto se puede entregar sin ellos y sin rehacer nada.
