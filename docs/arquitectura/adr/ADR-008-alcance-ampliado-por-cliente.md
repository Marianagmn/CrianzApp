# ADR-008 — Incluir muro, role-play, gamificación y WhatsApp por solicitud de la investigadora

- **Estado:** Propuesta (aceptar en la semana 1)
- **Fecha:** 2026-11-27
- **Decisores:** Mariana Gordillo, Julián Cristancho; investigadora informada en el acta de la semana 3
- **Sustituye parcialmente:** ADR-006 (canales diferidos) y la exclusión de RF-22, RF-23 y RF-24 en `00-diseno-base-v1.md` §3
- **Requisitos relacionadas:** RC-13, RC-16, RC-17, RC-18

## Contexto y problema

ADR-006 dejó WhatsApp, foro y role-play fuera del MVP y decía que se reabriría «con caso de uso». El documento de la investigadora ([01-requisitos-cliente.md](../../requisitos/01-requisitos-cliente.md)) es ese caso de uso: pide explícitamente el muro de conexión, el simulador de role-play, la gamificación y los boosters por WhatsApp como parte del estudio de adherencia.

## Opciones consideradas

1. **Mantener ADR-006.** Menos riesgo, pero el producto no responde a lo que pidió la cliente.
2. **Construir todo como núcleo.** Responde a la cliente, pero si un módulo de riesgo se atrasa, se cae la entrega completa.
3. **Construirlos después del núcleo, detrás de *feature flags* y con controles propios.**

## Decisión

Opción 3. Los cuatro módulos se construyen entre las semanas 11 y 13, cada uno detrás de una bandera apagada por defecto y con estos límites:

| Módulo | Límite obligatorio |
|---|---|
| Muro de conexión | Pre-moderación por facilitador, reportes, bloqueo, seudónimo no enlazable, rate limit, sanitización de HTML |
| Role-play | Solo escenarios curados; se presenta como ficción; si el padre revela un riesgo real se sale de la simulación y se activa el flujo de seguridad; suite de red-team propia |
| Gamificación | Reglas evaluadas en el servidor (el cliente no puede otorgarse insignias); sin rankings entre participantes |
| WhatsApp | Sin datos sensibles en el mensaje; enlaces firmados con expiración; opt-in por canal; envío manual o click-to-chat salvo presupuesto para la API oficial |

## Consecuencias

- **Beneficios:** se cubre lo que pidió la investigadora sin poner en riesgo el núcleo (identidad, consentimiento, biblioteca, chat, check-ins, dashboard).
- **Costes:** tres semanas de alcance extra y una suite de pruebas de seguridad por módulo.
- **Seguridad/privacidad:** el muro y el role-play son las superficies de mayor riesgo (contenido de terceros, jailbreak). Ninguno se activa con participantes reales sin las reglas firmadas (SUP-11, SUP-12).
- **Migración:** si el tiempo no alcanza, se entrega con las banderas apagadas; nada del núcleo depende de estos módulos.

## Revisión

Reabrir si la investigadora retira alguno de los módulos, si el comité de ética no los aprueba o si a la semana 10 el núcleo no está terminado.
