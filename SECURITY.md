# Política de seguridad

Responsable: **Julián Cristancho** (dueño del área de seguridad). Mariana revisa lo que toque el asistente de IA.

## Reportar una vulnerabilidad

No abras un issue público. Usa **Security → Report a vulnerability** (aviso privado de GitHub) en este repositorio, con:

- qué componente o ruta afecta,
- cómo reproducirlo (con datos sintéticos),
- qué impacto crees que tiene.

Se responde en un máximo de 5 días hábiles con una evaluación y, si aplica, un plan de corrección.

## Alcance

La aplicación web, la API, el asistente de IA (incluidos prompt injection y fuga de instrucciones o datos), la configuración de despliegue y la CI.

## Reglas de datos

- Solo datos sintéticos en desarrollo, pruebas y staging hasta tener el aval de ética institucional.
- No se guardan conversaciones completas del asistente por defecto ([ADR-005](docs/arquitectura/adr/ADR-005-no-transcripciones-por-defecto.md)).
- La identidad (correo) y los datos del estudio (seudónimo) viven en tablas separadas.
- Los logs no incluyen preguntas, respuestas, contraseñas, tokens ni datos personales.
- Los secretos van en variables de entorno o en los secrets de la plataforma, nunca en el repositorio; se rotan ante cualquier exposición.

## Controles

Modelo de amenazas en `docs/seguridad/modelo-amenazas.md` (STRIDE + OWASP Top 10 for LLM Applications). Objetivo de verificación: OWASP ASVS nivel 2 en los flujos esenciales. En CI: gitleaks, bandit, pip-audit y, desde la semana 10, OWASP ZAP baseline.
