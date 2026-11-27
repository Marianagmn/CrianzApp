# Cómo contribuir

Reglas de trabajo de Mariana y Julián. Si una regla estorba, se cambia en un PR, no se ignora.

## Flujo

1. Todo empieza en un **issue** del tablero, enlazado a un `RC-xx` (requisito de la investigadora), `RF-xx` o ADR, con criterio de aceptación *Given/When/Then*.
2. Rama desde `main`:
   - `feat/<rc-o-tema>` funcionalidad · `fix/<tema>` corrección · `sec/<tema>` control de seguridad · `docs/<tema>` documentación · `test/<tema>` solo pruebas.
3. PR pequeño (idealmente < 400 líneas), con la plantilla completa.
4. **Revisión obligatoria del otro**. Si toca un área con dueño ([plan §2](docs/plan/plan-de-trabajo.md#2-roles)), la aprueba el dueño.
5. CI en verde. Se fusiona con *squash*.

`main` está protegida: sin push directo, sin fusionar con la CI en rojo.

## Commits

[Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/) en español:

```
feat(biblioteca): filtrar micro-módulos por principio TBRI
fix(auth): no revelar si el correo existe en la recuperación
sec(asistente): redactar teléfonos antes de enviar al LLM
docs(adr): ADR-009 sesión en servidor
test(authz): cuenta A no lee check-ins de B
```

Tipos: `feat`, `fix`, `sec`, `docs`, `test`, `refactor`, `chore`, `ci`.

## Definición de terminado

Una tarea está terminada cuando:

- [ ] Cumple el criterio de aceptación del issue.
- [ ] Tiene pruebas: unitarias e integración, y **negativas** si hay autorización (otro usuario, otro rol, otra cohorte).
- [ ] La autorización se verifica en el backend, por objeto y alcance.
- [ ] No guarda ni registra en logs texto de conversaciones, secretos ni datos personales que no estén en el diccionario de datos.
- [ ] Si cambia el esquema, trae migración de Alembic reversible.
- [ ] Si tiene UI: funciona a 320 px, con teclado, y pasa axe-core sin violaciones serias.
- [ ] Si toca IA: prompt versionado y evaluación actualizada en `docs/ia/`.
- [ ] La documentación afectada (README, ADR, `docs/`) se actualiza **en el mismo PR**.

La versión completa está en [05-roadmap-validacion.md §5](docs/arquitectura/05-roadmap-validacion.md#5-definition-of-done-dod).

## Pruebas

| Carpeta | Qué prueba | Red externa |
|---|---|---|
| `tests/unit/` | Dominio y casos de uso | No |
| `tests/integration/` | BD, migraciones, índice RAG | No (PostgreSQL de prueba) |
| `tests/authz/` | Matriz de permisos, BOLA/IDOR | No |
| `tests/security/` | CSRF, sesión, cabeceras, XSS, subidas, prompt injection | No |
| `tests/conversation/` | Golden set y casos de comportamiento del asistente | No (LLM simulado) |
| `tests/e2e/` | Recorridos con Playwright, responsive y accesibilidad | No |
| `evaluation/` | Ragas y evaluaciones con LLM real | **Sí**: se corre a mano, no en CI |

## Decisiones

Toda decisión con alternativas y consecuencias se registra como ADR en `docs/arquitectura/adr/` con la [plantilla](docs/arquitectura/adr/ADR-template.md). Una ADR aceptada no se edita: se crea otra que la sustituye.

## Lo que nunca va al repositorio

Archivos `.env`, claves, bases de datos, exportaciones, conversaciones, datos de personas reales, el índice vectorial generado. gitleaks lo bloquea en `pre-commit` y en CI.
