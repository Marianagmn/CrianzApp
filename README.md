# CrianzApp

Plataforma web de apoyo a un programa de parentalidad basado en **TBRI®** (Intervención Relacional Basada en la Confianza) para cuidadores de niños y niñas en primera infancia. Acompaña el programa sincrónico con micro-módulos, check-ins, un asistente con IA acotada y un panel de monitoreo para el equipo facilitador, y produce los datos de adherencia que necesita el estudio.

Trabajo de grado de **Mariana Alejandra Gordillo Meneses** y **Julián David Cristancho Niño**.

> **Estado:** semana 1 de 16 (fundaciones). Aún no hay código de la aplicación; ver el [plan de trabajo](docs/plan/plan-de-trabajo.md).
>
> **Datos:** solo datos sintéticos hasta contar con el aval de ética institucional. CrianzApp no es un servicio de diagnóstico ni de atención de crisis.

## Qué hace

| Para el cuidador | Para el equipo facilitador e investigación |
|---|---|
| Onboarding con marco TBRI, registro mínimo y consentimiento por finalidad | Dashboard de adherencia digital (activa y pasiva) y asistencia a sesiones |
| Biblioteca de micro-módulos (10–15 min) por principio: Empoderamiento, Conexión, Corrección | Alertas de escalamiento humano por riesgo, estrés elevado o inactividad |
| Chat con IA que responde solo con la guía, cita la fuente y lleva al módulo correspondiente | Métricas del motor de IA: consultas por principio, escalamiento, autonomía |
| Check-ins de 1–3 minutos: estrés en 1 clic y práctica en el hogar | Escalas de usabilidad y aceptabilidad (SUS, UES-SF, WAI-SR, NPS) |
| Simulador de role-play, gimnasio relacional e insignias* | Export seudonimizado para el análisis del estudio |
| Muro de conexión moderado y recordatorios por WhatsApp* | Moderación del muro |

\* Módulos de la fase 4, detrás de *feature flags* ([ADR-008](docs/arquitectura/adr/ADR-008-alcance-ampliado-por-cliente.md)).

## Stack

Python 3.12 · Flask 3 (monolito modular, Jinja) · PostgreSQL 16 + SQLAlchemy 2 + Alembic · Chroma + BM25 para el RAG · Groq (tool calling) · pytest + Playwright · CI con ruff, bandit, pip-audit, gitleaks y OWASP ZAP. Justificación en [ADR-001](docs/arquitectura/adr/ADR-001-monolito-flask.md) a [ADR-007](docs/arquitectura/adr/ADR-007-stack-y-herramientas.md).

## Equipo

| | Área de la que es dueña/o |
|---|---|
| **Mariana** | IA y visual computing: asistente (RAG y agente), evaluación, clasificadores, simulador, visualización de datos, sistema de diseño |
| **Julián** | Ciberseguridad: identidad y acceso, consentimiento y privacidad, auditoría, seguridad del asistente, moderación, CI de seguridad, pentests |

El resto se trabaja en carga compartida y equitativa; las reglas están en el [plan](docs/plan/plan-de-trabajo.md#2-roles).

## Documentación

| Documento | Para qué |
|---|---|
| [Plan de trabajo](docs/plan/plan-de-trabajo.md) | Las 16 semanas: quién hace qué, qué se necesita del otro, pruebas y documentación |
| [Requisitos de la investigadora](docs/requisitos/01-requisitos-cliente.md) | Lo que pidió, trazado a requisitos y semanas |
| [Supuestos y pendientes](docs/requisitos/02-supuestos-y-pendientes.md) | Lo que no definió y el valor con el que avanzamos |
| [Migración del chatbot](docs/plan/migracion-chatbot.md) | Cómo entra el agente del Avance 3 |
| [Arquitectura](docs/arquitectura/00-LEEME.md) | Diseño base, especificación, API, privacidad, roadmap y ADR |
| [CONTRIBUTING](CONTRIBUTING.md) | Ramas, commits, PR y definición de terminado |
| [SECURITY](SECURITY.md) | Cómo reportar una vulnerabilidad y reglas de datos |

## Origen

El asistente viene del repositorio `AsistenteCrianza` (avances 2 y 3 del curso de IA): RAG con evaluación Ragas y luego un agente con tool calling y búsqueda web controlada. Su incorporación está en el [plan de migración](docs/plan/migracion-chatbot.md).
