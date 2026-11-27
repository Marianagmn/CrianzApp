# 00. Diseño base v1.0 — decisiones técnicas del MVP

**Estado del documento:** base recomendada para aprobar con tutor y equipo. No representa aprobación del cliente ni autorización para tratar datos reales.

## 1. Enunciado del producto

Plataforma web ligera, responsive y supervisable que refuerza un programa de parentalidad basado en TBRI, ayuda a los cuidadores a consultar contenido aprobado y practicar herramientas, permite check-ins definidos por el protocolo y produce métricas de uso/adherencia con separación de datos. Facilita escalamiento humano bajo un procedimiento operativo real. **No es un terapeuta digital ni un sistema de diagnóstico, y no debe presentarse como monitor de crisis en tiempo real sin cobertura humana demostrable.**

## 2. Diseño técnico que recomendamos congelar

| Decisión | Base v1.0 | Motivo y límite |
|---|---|---|
| Arquitectura | Monolito modular Flask por capas | Reutiliza el prototipo y reduce complejidad de despliegue; evitar microservicios. |
| Frontend MVP | Jinja + HTML/CSS/JS progresivo | Mantener el frontend actual; priorizar responsive/accesibilidad. React/SPA no se adopta sin requisito que lo justifique. |
| Capas | Presentación → aplicación → dominio; infraestructura implementa puertos definidos por aplicación | Mantener negocio independiente de Flask, ORM, Chroma y proveedor LLM. |
| Persistencia operativa | PostgreSQL como fuente de verdad; SQLAlchemy + migraciones versionadas como opción base a formalizar en ADR | Cuentas, permisos, consentimientos, asignaciones, contenidos, actividades y eventos estructurados requieren consistencia transaccional. |
| Índice RAG | Chroma como índice derivado del corpus aprobado; nunca como base de usuarios | Separación del dominio, reconstrucción reproducible y versionado del contenido. |
| API | Contratos nuevos bajo `/api/v1`; errores uniformes; no depender de alias duplicados actuales | Evitar tratar rutas de demo como API estable. Mantener adaptador temporal solo si se confirma consumidor externo. |
| Identidad y acceso | Sesiones seguras para la web y autorización backend por rol + alcance + relación con el participante | Denegar por defecto; no confiar en ocultar botones en UI. |
| Contenido | Corpus y guiones cerrados, aprobados, versionados y retirables | Responder con evidencia; citar fuente/versión; abstenerse si falta evidencia. |
| Generación externa | Desactivada por defecto hasta aprobación institucional/proveedor y evaluación | La pregunta, historial y fragmentos pueden salir del sistema; usar modo local/extractivo como punto de partida. |
| Transcripciones | No persistir conversaciones completas por defecto | Guardar metadatos mínimos de uso/calidad solo si protocolo y evaluación lo autorizan. No enviar texto a logs por defecto. |
| Riesgo | Guiones preaprobados y señales revisables por humanos; no diagnóstico automático | Las palabras clave no comprenden contexto, negación ni eufemismo y no sustituyen un protocolo profesional. |
| Métricas | Tres dominios separados: operación técnica, uso/adherencia y variables de investigación | Evitar confundir interacción con resultado clínico o causalidad. Definir denominadores antes del estudio. |
| Seguridad de publicación | Separación de autor, revisor y publicador; historial inmutable y rollback | Evita que contenido no validado se despliegue a participantes. |
| Calidad | pytest, CI reproducible, pruebas negativas de permisos, seguridad, RAG, responsive y accesibilidad | El estado “pasa” requiere evidencia; la auditoría actual fue estática y no ejecutó pruebas. |
| Despliegue | Entorno local/test/staging separados; alojamiento de producción solo con DB persistente, backups, gestión de secretos y acuerdos revisados | El blueprint gratuito observado no es base aprobada para datos de participantes. |

### Stack recomendado

- Backend: versión soportada de Python compatible con dependencias verificadas; Flask y Gunicorn.
- UI: Jinja, CSS y JavaScript actual, mejorados.
- Base relacional: PostgreSQL.
- ORM/migraciones: SQLAlchemy y Alembic (formalizar con ADR antes de implementarlos).
- Pruebas: pytest; pruebas de navegador cuando los flujos principales estén definidos.
- RAG: conservar loaders/Chroma actuales después de probarlos; evaluar embeddings y búsqueda con benchmark, no cambiar modelo por intuición.
- Calidad de código/seguridad: lint/type checks compatibles con el repo, lockfile reproducible, escaneo de dependencias y secretos en CI.
- Contratos: OpenAPI versionado para `/api/v1` cuando los endpoints se vayan implementando.

No fijar versiones concretas hasta ejecutar un baseline, confirmar la versión de Python y resolver incompatibilidades observadas.

## 3. Límite funcional del MVP propuesto

### Dentro del producto objetivo, sujeto a definición de protocolo y validación

1. Registro/login, roles y permisos.
2. Consentimientos separados y onboarding mínimo aprobado.
3. Programa/cohortes/sesiones según el estudio.
4. Biblioteca de materiales aprobados y tarjetas de práctica.
5. Check-ins estructurados, sin interpretación clínica automática.
6. FAQ RAG cerrado, trazable, evaluado y con abstención.
7. Flujos guiados preaprobados para situaciones inmediatas.
8. Solicitud/escalamiento humano con expectativas reales de cobertura.
9. Panel técnico con métricas agregadas; panel de investigación/profesional solo según permisos y protocolo.
10. Auditoría, retención, solicitudes de titulares, backups y pruebas.

**Importante:** la inclusión en esta lista define dirección de producto, no habilita implementación de campos de cuestionario, consentimientos ni protocolo de riesgo hasta que los responsables los aprueben.

### Excluido del MVP

- Foro comunitario.
- WhatsApp y SMS. Ejecutar estudio de factibilidad y costos primero.
- Avatar.
- Role-play generativo abierto.
- Perfil o puntuación psicológica automatizada.
- Predicción de “horas difíciles” y recomendaciones conductuales inferidas.
- Diagnóstico, triaje clínico autónomo o promesa de supervisión 24/7.
- Aplicación móvil nativa o SPA React sin necesidad demostrada.
- Microservicios.

El correo transaccional puede añadirse después de aprobación del mensaje, proveedor, opt-in, seguridad y preferencias; no debe bloquear el primer incremento técnico.

## 4. Reglas de datos no negociables por diseño

1. Separar identidad/contacto del ID y dataset de investigación.
2. No crear un perfil con nombre, fecha exacta de nacimiento o historial del niño como objeto por defecto.
3. No mostrar respuestas individuales de caracterización en el panel del administrador técnico.
4. Resumen profesional estructurado y mínimo; no generar perfil clínico con IA.
5. No persistir prompts, preguntas, respuestas, contactos ni datos infantiles en logs por defecto.
6. Una tabla de identidad↔seudónimo solo existe si se justifica, con custodia/acceso separado y periodo definido.
7. No usar datos reales hasta el gate institucional de ética, privacidad y operación.
8. Los derechos, retención, borrado/supresión, backups, transferencias y subencargados se definen con asesoría institucional; el software no decide qué obligación legal prevalece.

## 5. Decisiones que no puede cerrar el equipo técnico unilateralmente

- Preguntas, escalas, frecuencia y reglas de onboarding/check-in.
- Variables de estudio, objetivo primario de adherencia, cohortes y ventanas de medición.
- Consentimientos, responsable/encargado, base de tratamiento, periodo de retención y tratamiento de relatos sensibles.
- Corpus oficial: derechos, permiso de uso, aprobador profesional, versión autorizada y procedimiento de retiro.
- Mensajes/ramas de riesgo, números oficiales, disponibilidad humana, horario, SLA, suplencias y parada del piloto.
- Qué datos puede ver psicología y qué variables puede exportar investigación.
- Si se permiten proveedores externos de IA y transferencias internacionales.
- Canales de contacto y presupuesto para email/WhatsApp/SMS.

## 6. Gate antes de tratar datos de participantes

No iniciar un piloto con participantes hasta documentar: protocolo y revisión/aprobación institucional aplicable; inventario y finalidad de datos aprobados; textos y evidencia de consentimiento aprobados; responsable/encargado y acuerdos con proveedores; configuración de acceso por roles; periodo de retención y derechos; corpus/guiones revisados; responsable de escalamiento con fuera de horario; pruebas de autorización/privacidad/recuperación superadas; y aprobación de despliegue y rollback.

## 7. Criterio de cierre de diseño

El diseño técnico se considera suficientemente cerrado cuando el equipo acepta esta base y registra ADR iniciales. Los requisitos del cliente se consideran preparados para implementación cuando cada pendiente de `07-matriz-de-pendientes.md` está resuelto, aprobado por la persona responsable y asociado a requisitos/criterios de aceptación. No es necesario tener todas las respuestas clínicas para diseñar los límites del sistema, pero sí antes de implementar las funcionalidades que dependen de ellas.
