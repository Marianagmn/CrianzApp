# ADR-007 — Stack técnico y herramientas de calidad

- **Estado:** Propuesta (aceptar en la semana 1)
- **Fecha:** 2026-11-27
- **Decisores:** Mariana Gordillo, Julián Cristancho; tutor informado
- **Requisitos/ADR relacionadas:** ADR-001 (monolito Flask), ADR-002 (Jinja), ADR-003 (PostgreSQL + Chroma), RNF-SEC-01..03, RNF-MAINT-01

## Contexto y problema

ADR-001 a 003 fijan la forma (monolito Flask, Jinja, PostgreSQL + Chroma) pero no versiones ni herramientas. `00-diseno-base-v1.md` pedía no fijarlas hasta tener un baseline. El baseline ya existe: el chatbot del Avance 3 corre en Python 3.12 con Flask, Chroma y Groq, y en Python 3.14 falla Ragas. Hay que fijarlas antes de escribir código.

## Opciones consideradas

1. **Flask + SQLAlchemy 2 + Alembic.** Reutiliza el chatbot tal cual; SQLAlchemy da migraciones versionadas y reversibles. Requiere armar a mano auth, CSRF y formularios con extensiones.
2. **Django.** Trae auth, admin, CSRF y migraciones. Obliga a reescribir el chatbot y contradice ADR-001.
3. **FastAPI + SPA.** Mejor tipado de la API, pero contradice ADR-002 y suma un segundo frontend.

## Decisión

Opción 1, con estas piezas:

| Área | Elección |
|---|---|
| Lenguaje | Python 3.12 (fijado en `.python-version`) |
| Web | Flask 3, app factory, blueprints, Jinja; Gunicorn |
| Datos | PostgreSQL 16; SQLAlchemy 2; Alembic |
| Auth y seguridad web | Flask-Login (sesión), Flask-WTF (CSRF), Flask-Limiter, Flask-Talisman (cabeceras/CSP), argon2-cffi |
| IA | Groq (tool calling) y Chroma reutilizados del Avance 3, detrás de puertos; Gemini solo como juez de Ragas |
| Frontend | Jinja + CSS propio con tokens de diseño + JS progresivo; Chart.js o Vega-Lite para el dashboard |
| Pruebas | pytest, pytest-cov, Playwright (E2E, responsive, axe-core para accesibilidad) |
| Calidad estática | ruff (lint + formato), mypy en `domain/` y `application/` |
| Seguridad en CI | gitleaks (secretos), bandit (SAST), pip-audit (dependencias), OWASP ZAP baseline (DAST, desde la semana 10) |
| Entornos | docker compose local (app + postgres); staging en Railway (ya usado en el Avance 3) |
| Dependencias | `requirements.in` → `requirements.txt` con hashes (pip-tools); runtime y evaluación separados |

## Consecuencias

- **Beneficios:** el chatbot migra sin reescribirse; cada control de seguridad tiene una herramienta que lo prueba en CI.
- **Costes:** más extensiones de Flask que mantener; mypy solo en las capas internas al principio.
- **Seguridad/privacidad:** gitleaks y pip-audit bloquean el merge si fallan.
- **Migración:** `src/` del Avance 3 entra por `infrastructure/knowledge` y `application/assistant` ([plan de migración](../../plan/migracion-chatbot.md)).

## Revisión

Reabrir si la institución exige otro hosting o motor de BD, o si una dependencia crítica deja de tener soporte para Python 3.12.
