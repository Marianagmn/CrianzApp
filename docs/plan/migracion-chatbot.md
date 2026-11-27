# Migración del chatbot de crianza (Avance 3) a CrianzApp

- **Origen:** repositorio `AsistenteCrianza` (agente con tool calling del Avance 3: RAG híbrido BM25 + Chroma, tools `buscar_en_base`, `generar_referencia_apa`, `buscar_en_web`, clasificación de riesgo determinística, evaluación Ragas).
- **Destino:** módulo `assistant` de CrianzApp, detrás de un puerto (`AssistantPort`), según [03-arquitectura-y-api.md](../arquitectura/03-arquitectura-y-api.md).
- **Dueña:** Mariana. **Revisión de seguridad:** Julián en M2, M3, M5 y M6.

## Principios

1. **No se reescribe, se traslada con sus pruebas.** Cada pieza entra al repo nuevo con los tests que ya tenía, y se mueve solo cuando esos tests pasan.
2. **El chatbot no conoce Flask ni la BD.** Se le inyectan el índice, el cliente LLM y el reloj; así se prueba sin red.
3. **Cambia lo que el Avance 3 hacía mal para producción:** el historial lo mandaba el navegador; no había autenticación ni rate limit; el agente perdió fidelidad de citas frente al pipeline fijo.
4. **El repo viejo no se borra.** Queda etiquetado y en solo lectura como evidencia del Avance 3.

## Qué se migra y qué no

| Del Avance 3 | Destino en CrianzApp | Decisión |
|---|---|---|
| `src/knowledge_base.py` (BM25 + Chroma, chunking) | `infrastructure/knowledge/` | Migrar con tests |
| `src/tools.py` | `application/assistant/tools/` | Migrar; agregar `recomendar_modulo` en S7 |
| `src/agent.py`, `agent_system_prompt.py`, `agent_few_shot.py` | `application/assistant/agent/` | Migrar; corregir formato de citas en S6 |
| `src/safety.py` | `domain/safety/` (patrones) + `application/safety/` (señales) | Migrar; se complementa con clasificador en S9 |
| `src/output_formatter.py` | `application/assistant/contract.py` | Migrar |
| `src/rag_chat.py`, `prompt_builder.py`, `system_prompt.py`, `few_shot_examples.py` (pipeline fijo del Avance 2) | `application/assistant/pipeline_fijo/` | Migrar solo como referencia de evaluación; no se expone en la API |
| `scripts_ragas/` y golden set | `evaluation/` + `tests/conversation/` | Migrar; dependencias de evaluación separadas de las de runtime |
| `docs/base_conocimiento/` (corpus) | `corpus/` con registro de versión, checksum y aprobación | Migrar tras revisar derechos de uso de los PDF de TBRI |
| `taller_multimodal/` (visión y audio) | `infrastructure/llm/vision.py` | Reutilizar en S5 para alt-text |
| `main.py` (composición global) | — | **No migrar**: lo reemplaza `create_app()` |
| `templates/`, `static/` | — | **No migrar**: la UI se rehace con el sistema de diseño; se conserva la idea de fuentes desplegables y herramientas usadas |
| Historial enviado por el cliente | — | **No migrar**: el historial pasa al servidor con expiración (ADR-010) |
| `vectorstore/`, resultados JSON, `.env` | — | **No migrar**: se regeneran; los secretos se rotan en S1 |

## Etapas

| Etapa | Semana | Qué se hace | Listo cuando |
|---|---|---|---|
| **M0** · Congelar | S1 | Tag `avance-3-final` en `AsistenteCrianza`; guardar la línea base (Ragas del pipeline fijo y del agente, 7 casos de comportamiento) en `docs/ia/linea-base-avance3.md` | La línea base tiene números reproducibles y el commit exacto |
| **M1** · Trasladar | S3 | Crear `AssistantPort`; mover índice, tools, agente y safety con sus tests; el LLM se inyecta y en CI se simula | Los tests del Avance 3 pasan en el repo nuevo sin acceso a internet |
| **M2** · Integrar | S6 | Chat dentro de la app autenticada; historial en servidor con TTL; rate limit, cuota, redacción de datos personales, lista blanca de salida; evento `AssistantTurn` sin texto | Un cuidador conversa con sesión y no queda texto en BD ni en logs |
| **M3** · Gobernar el corpus | S6 | Registro de documentos con versión, checksum y aprobación; `flask corpus rebuild` reproducible; solo se indexa lo aprobado | Retirar un documento hace que deje de citarse |
| **M4** · Conectar con la biblioteca | S7 | Tool `recomendar_modulo` y citas enlazadas a `/biblioteca/<modulo>` (RC-08, RC-09); clasificador de principio TBRI por turno (RC-22.1) | Una respuesta recomienda un micro-módulo publicado y el turno queda etiquetado por principio |
| **M5** · Escalar a humanos | S9 | El agente crea `SafetySignal` (solo categoría) que llega a la cola del facilitador | Una frase de riesgo en el chat aparece en la cola del facilitador asignado y en ninguna otra |
| **M6** · Reutilizar para el simulador | S11 | El role-play usa la misma infraestructura (cliente LLM, safety, contrato de salida) con su propio prompt y límites | El simulador no duplica código del agente |
| **M7** · Re-evaluar | S14 | Ragas, golden set y casos de comportamiento sobre la versión integrada frente a la línea base M0 | Informe de evaluación final con la comparación |

## Deuda conocida que se paga en la migración

1. **Fidelidad de citas del agente** (Avance 3: faithfulness 0,07 del agente frente a 0,54 del pipeline fijo). Hipótesis: las citas en prosa libre `(Fuente: documento, p.X)` se evalúan peor que las numeradas. Se corrige en M2 y se vuelve a medir.
2. **Abstención fuera de dominio del agente**: corregida en el Avance 3 por prompt y verificada a mano (3/3), pero sin re-correr la evaluación completa por la cuota de Groq. Se re-mide en M2.
3. **Detección de riesgo solo con expresiones regulares**: se complementa con un clasificador evaluado en M5.
4. **Cuota de Groq**: el límite diario es por organización, no por clave. Las evaluaciones reales se programan con margen y en CI se usa el LLM simulado.
