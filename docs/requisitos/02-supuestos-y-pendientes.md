# 02. Supuestos y pendientes

Lo que la investigadora no definió y hace falta para construir. Cada supuesto tiene un **valor por defecto** con el que el equipo avanza, de modo que nadie se queda bloqueado esperando una respuesta.

## Cómo se usan

Hay dos tipos y se tratan distinto:

| Tipo | Qué cubre | Regla |
|---|---|---|
| **E — Decisión del equipo** | Producto, UX y técnica: frecuencias, umbrales operativos, formatos, criterios de insignias | Se implementa el valor por defecto y se informa en el acta de la demo. Si ella no lo objeta en **5 días hábiles**, queda adoptado. Si después pide otra cosa, se cambia (todo es configurable). |
| **A — Requiere su aval** | Ético, clínico y legal: consentimiento, protocolo de riesgo, instrumentos y outcomes, reglas del muro, escenarios del role-play | Se construye con texto marcado **BORRADOR** y datos sintéticos. **El silencio no cuenta como aprobación.** Nada tipo A llega a participantes reales sin su firma en un acta ([plantilla](../arquitectura/plantillas/acta-validacion-requisitos.md)). |

Para que cambiar un valor sea barato, todo lo de esta tabla vive en **configuración versionada** (YAML/BD), no en el código.

## Matriz

| ID | Tipo | Tema | Lo que falta | Valor por defecto del equipo | Afecta a | Estado |
|---|---|---|---|---|---|---|
| SUP-01 | A | Instrumentos de outcomes | No nombró escalas de prácticas disciplinarias, estrés parental ni relación niño-cuidador | Motor de cuestionarios genérico con 3 instrumentos *placeholder* sintéticos. Candidatos a validar con ella, revisando licencia y validación en español: CPRS (relación), PSI-SF (estrés, comercial), CTSPC (prácticas, con licencia) | RC-23 | Pendiente |
| SUP-02 | E | Frecuencia de check-ins | «Periódicas» sin cadencia | Máx. 1 al iniciar sesión por día + 1 al completar cada micro-módulo; siempre se puede omitir | RC-10 | Por adoptar |
| SUP-03 | E | Estrés «elevado» | Sin umbral | Escala 1–5; alerta si ≥4 en dos check-ins seguidos o 5 una vez | RC-11, RC-15 | Por adoptar |
| SUP-04 | E | Inactividad «prolongada» | Sin plazo | 7 días sin evento activo → alerta de seguimiento (no de riesgo) | RC-15 | Por adoptar |
| SUP-05 | A | Caracterización mínima | No lista los campos | Cuidador: rango de edad, relación con el niño, nivel educativo (rangos). Niño: rango de edad en años. Nada de nombre, documento, dirección ni fecha exacta | RC-02 | Pendiente |
| SUP-06 | E | Población | El estudio dice «primera infancia»; el chatbot del Avance 3 decía 0–12 años | 0–6 años en textos y onboarding; el corpus TBRI se mantiene | RC-02, RC-07 | Por adoptar |
| SUP-07 | E | Asistencia a sesiones sincrónicas | No dice cómo se registra | El facilitador marca asistencia en el panel; sin integración con Zoom/Meet | RC-20.1 | Por adoptar |
| SUP-08 | E | Videos | No dice quién los produce ni dónde se alojan | Ella los entrega; se embeben como video no listado o archivo en storage. Mientras tanto, placeholder | RC-01, RC-06 | Por adoptar |
| SUP-09 | E | Envío por WhatsApp | No define proveedor ni presupuesto | v1: enlaces *click-to-chat* y plantillas que envía el facilitador, con enlaces firmados de regreso a la web. API oficial de WhatsApp solo si hay presupuesto aprobado | RC-13 | Por adoptar |
| SUP-10 | A | Puntos de corte e instrumento exacto de usabilidad del chatbot | Columna de benchmarks vacía; nombra «ITA-BUS B / uMARS» | Reportar puntajes crudos y la referencia publicada de cada escala (p. ej. SUS 68 como promedio de referencia). Confirmar qué escala de chatbot usa | RC-21 | Pendiente |
| SUP-11 | A | Moderación del muro | «Moderación activa» sin regla | Pre-moderación: nada se publica sin aprobación de un facilitador. Reportes y bloqueo disponibles | RC-18 | Pendiente |
| SUP-12 | A | Escenarios del role-play | No hay banco de micro-casos | 6 escenarios de ejemplo (2 por principio) derivados del corpus, marcados BORRADOR hasta su revisión | RC-16 | Pendiente |
| SUP-13 | E | Dashboard «en tiempo real» | No define latencia | Datos al cargar la página y refresco cada 5 min; sin websockets | RC-14 | Por adoptar |
| SUP-14 | E | Criterios de insignias | Solo da nombres de ejemplo | «Guardián de la Conexión»: 7 retos de conexión completados. «Maestro de la Regulación»: módulos de Empoderamiento + 5 check-ins. Resto en la semana 12 | RC-17 | Por adoptar |
| SUP-15 | E | Idioma y accesibilidad | No lo menciona | Español (Colombia); objetivo WCAG 2.2 AA en flujos esenciales | Todo | Por adoptar |
| SUP-16 | E | Roles | No define quién es «facilitador» ni «moderador» | Roles: cuidador, facilitador, investigador, administrador técnico, editor de contenido. El facilitador modera el muro | RC-14, RC-18 | Por adoptar |
| SUP-17 | A | Textos de consentimiento y política de datos | No los entregó | Plantilla por capas con finalidades separadas (servicio, investigación, contacto, WhatsApp), marcada BORRADOR | RC-03 | Pendiente |
| SUP-18 | A | Protocolo de escalamiento | No define responsables, horarios ni líneas | Borrador en `docs/seguridad/protocolo-escalamiento.md` (S9): quién atiende, horario, qué ve el cuidador fuera de horario, líneas 141/123/106 a verificar | RC-15 | Pendiente |
| SUP-19 | A | Datos reales | — | **Solo datos sintéticos** hasta tener aval de ética institucional | Todo | Vigente |

## Preguntas para la próxima reunión

Para no hacerle perder tiempo, se le llevan **decisiones tomadas** para que confirme o corrija, no preguntas abiertas:

1. «Para estrés elevado usaremos ≥4 en dos check-ins seguidos. ¿Le sirve o prefiere otro umbral?» (SUP-03)
2. «¿Qué escalas usará para prácticas disciplinarias, estrés y relación? Si no las tiene, proponemos estas tres.» (SUP-01)
3. «Necesitamos los textos de consentimiento y el protocolo de escalamiento firmados antes de cualquier prueba con personas.» (SUP-17, SUP-18)
4. «¿Nos envía 3 micro-módulos reales, uno por principio, para la semana 5?» (RC-05)
5. «¿La escala de usabilidad del chatbot es la BUS o la uMARS?» (SUP-10)
