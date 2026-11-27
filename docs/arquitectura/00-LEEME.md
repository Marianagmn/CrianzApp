# Arquitectura — índice

Paquete de diseño preparado en octubre de 2026 y ampliado con los requisitos de la investigadora en noviembre. Si un documento contradice a otro, manda la ADR más reciente.

| Documento | Contenido |
|---|---|
| [00-diseno-base-v1.md](00-diseno-base-v1.md) | Decisiones técnicas del MVP y reglas de datos no negociables |
| [01-auditoria.md](01-auditoria.md) | Auditoría del prototipo del chatbot (octubre de 2026) |
| [02-especificacion-funcional.md](02-especificacion-funcional.md) | Requisitos RF-01..24, casos de uso e historias |
| [03-arquitectura-y-api.md](03-arquitectura-y-api.md) | Capas, paquetes, modelo de dominio y API `/api/v1` |
| [04-datos-privacidad-ia-seguridad.md](04-datos-privacidad-ia-seguridad.md) | Matriz de permisos, inventario de datos, IA responsable y STRIDE |
| [05-roadmap-validacion.md](05-roadmap-validacion.md) | Métricas, requisitos no funcionales, pruebas y DoD |
| [06-como-empezar.md](06-como-empezar.md) | Primeros incrementos, backlog y proceso Git |
| [07-matriz-de-pendientes.md](07-matriz-de-pendientes.md) | Redirige a los supuestos y pendientes vigentes |
| [adr/](adr/) | ADR-001..008 y plantilla |
| [plantillas/acta-validacion-requisitos.md](plantillas/acta-validacion-requisitos.md) | Acta para las demos con la investigadora |

**Cambio importante de noviembre:** el diseño base dejaba fuera del MVP el muro, el role-play, la gamificación y WhatsApp. La investigadora los pidió y [ADR-008](adr/ADR-008-alcance-ampliado-por-cliente.md) los incluye en la fase 4 con controles propios.
