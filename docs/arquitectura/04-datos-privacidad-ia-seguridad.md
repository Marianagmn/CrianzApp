# 04. Actores, permisos, datos, privacidad, IA y amenazas

## 1. Marco y postura

Este análisis es un **mapa preliminar de diseño**, no dictamen jurídico ni afirmación de cumplimiento. Debe revisarse contra el protocolo de investigación, roles institucionales, contratos, flujos reales, lugar de hosting y población efectivamente enrolada. Hay niños/as como población de interés y los participantes son cuidadores; se debe evitar recopilar datos de menores por defecto. Si aparecen datos sensibles, datos de salud o información de niños, elevar la revisión y documentar necesidad/interés superior/garantías con el comité y asesoría jurídica.

### Normas colombianas a evaluar con asesoría

| Referencia solicitada | Relevancia de diseño que debe validar asesoría |
|---|---|
| Ley 1581 de 2012 | Régimen de protección de datos personales: finalidades, autorización/base aplicable, deberes de responsables/encargados, derechos de titulares, seguridad/confidencialidad y tratamiento de datos sensibles. Definir responsable/encargado institucional y canales de consulta/reclamo. |
| Decreto 1074 de 2015 | Reglamentación compilada relevante para protección de datos, autorización, políticas/procedimientos, transmisión/transferencia y responsabilidad demostrada. Revisar texto vigente y aplicabilidad contractual/transfronteriza. |
| Ley 1098 de 2006 | Protección integral e interés superior/prevalencia de derechos de niños, niñas y adolescentes. Determinar si se tratan datos de menores y quién tiene facultad/representación; reducir datos y exposición. |
| Ley 1090 de 2006 | Marco de ejercicio profesional de psicología y deberes éticos cuando profesionales intervengan; delimitar aplicación a roles y servicios concretos con responsable institucional. |
| Resolución 8430 de 1993 | Clasificación y requisitos éticos de investigación en salud, consentimiento y revisión por comité según naturaleza del estudio. Comité debe determinar si aplica y clasificación de riesgo; no inferir por ser software. |

**No se concluye** qué autorización, consentimiento del cuidador/representante o asentimiento es jurídicamente suficiente, ni cuál categoría de riesgo aplica. El comité de ética y asesoría institucional deben decidirlo antes de enrolar, incluyendo tratamiento incidental de relatos, datos sensibles, investigación con menores, transferencia a terceros, grabaciones/transcripciones y retiro de datos.

## 2. Actores y matriz de permisos

Convención: **S** = permitido dentro del alcance y registrado; **A** = solo agregado/codificado; **N** = no permitido; **C** = condicionado a consentimiento, asignación, estudio o protocolo. Los permisos se implementan en backend, no solo en botones. Por defecto se niega; cada acción considera finalidad, tenant/estudio, asignación y vigencia.

| Acción/dato | Visitante | Cuidador participante | Psicólogo/facilitador | Investigador/coordinador | Admin técnico | Editor/validador | Moderador | Proveedor externo |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Ver páginas públicas | S | S | S | S | S | S | S | N |
| Gestionar su cuenta y sesiones propias | N | S | S propia | S propia | S técnica sin leer contenido | S propia | S propia | N |
| Ver/actualizar onboarding propio | N | C | N salvo resumen permitido | A/C | N | N | N | N |
| Ver contenidos publicados | N/C | S por cohorte | S | S | S técnico | S | S | N |
| Crear/revisar/publicar contenidos | N | N | C como revisor experto | C | N salvo despliegue técnico | C según separación de funciones | N | N |
| Ver participante asignado | N | S propio | C, mínimo necesario | C, codificado y protocolo | N en nivel individual | N | N | N |
| Ver solicitudes/señales | N | S propias/estado | C, asignados | C según protocolo | N salvo metadato agregado de operación | N | N | N |
| Administrar cuentas/infraestructura | N | N | N | N salvo tarea asignada | S técnica, sin respuestas de caracterización | N | N | N |
| Ver métricas | N | S propio progreso | A/C del equipo asignado | A/C por protocolo | A agregado únicamente | A editorial | A moderación, no investigación | N |
| Exportar investigación | N | N | N salvo tarea aprobada | C: variables codificadas y auditadas | N | N | N | N |
| Moderar comunidad | N | N | N | N | N | N | C mínimo necesario | N |
| Recibir datos como encargado/procesador | N | N | N | N | N | N | N | C, solo campos mínimos por contrato y autorización aprobada |

**Separación de funciones:** no crear rol “admin” universal. Un mismo individuo puede tener más de un grant solo con autorización explícita, alcance, justificación, temporalidad y auditoría. Admin técnico accede a configuración/estado agregado, nunca respuestas individuales de caracterización. Investigador opera sobre IDs codificados autorizados. Psicólogo ve participantes asignados y campos necesarios. Editor crea; validador distinto aprueba. Proveedor no tiene login interactivo ni acceso a DB; recibe payload explícito mínimo.

### Pruebas de acceso que deben denegarse

1. Usuario A intenta leer/editar onboarding, check-in, progreso o solicitud de B.
2. Psicólogo con participante no asignado consulta ID válido, inválido o de otra cohorte.
3. Admin técnico solicita endpoint/export de respuestas individuales; respuesta 403/404 y evento auditado sin exponer existencia.
4. Investigador intenta consultar contacto, correo, nombre o tabla de enlace identidad↔ID de estudio.
5. Editor intenta aprobar su propio contenido o publicar sin fuente/licencia/revisión.
6. Proveedor externo usa una clave técnica para solicitar registros internos; no hay interfaz/credencial con ese alcance.
7. Usuario con consentimiento de servicio, pero no de investigación/canal, llama endpoints de investigación/notificación.
8. Usuario inactivo, consentimiento retirado o vínculo expirado conserva cookie; los datos/acciones restringidos siguen bloqueados.
9. Cambiar `participant_id`, `study_id`, filtros, export path o identificador secuencial no habilita acceso (IDOR/BOLA).
10. Un agregado con conteos muy pequeños o filtros combinados se suprime para evitar reidentificación.

## 3. Inventario preliminar de datos

Retención concreta, campos de cuestionario, responsables y proveedores están **pendientes del protocolo y aprobación**. “No definido” no autoriza almacenamiento indefinido. Tabla de identidad se separa lógica y criptográficamente de datos analíticos; limitar acceso a la clave de reidentificación y registrar cada uso.

| Conjunto/campo candidato | Finalidad y necesidad | Sensibilidad/obligatorio | Consentimiento/base a validar | Roles/proveedores | Retención/eliminación propuesta | Riesgo de reidentificación |
|---|---|---|---|---|---|---|
| Cuenta: email/contacto y hash de credencial | Login, recuperación, comunicación indispensable | Identificador; email obligatorio solo si método elegido | Servicio; uso de contacto separado; base/legal revisada | Identity; proveedor de correo solo para token | Mientras cuenta activa + plazo institucional aprobado; borrar/anomizar al cerrar, salvo deber aplicable | Alto por contacto directo; guardar separado del estudio |
| `participant_id` aleatorio y enlace cuenta↔código | Vincular servicio con estudio | Seudónimo; estrictamente necesario si hay seguimiento | Consentimiento investigación/protocolo según aplique | Custodio de identidad limitado, investigador no por defecto | Custodia separada; calendario/llave definido por protocolo; destrucción verificable al fin | Muy alto si llave accesible; pequeños grupos permiten inferir |
| Cohorte, sesiones, fechas de actividad | Operar programa y medir adherencia | Identificador indirecto; mínimo | Servicio e investigación separados si usos distintos | Participante propio; investigador codificado; profesional asignado limitado | Ventana del programa + retención aprobada; agregados después de revisión | Medio/alto en cohortes pequeñas o fechas exactas |
| Respuestas de onboarding cerradas | Caracterización mínima y estratificación aprobada | Potencialmente sensible; opcional salvo justificación | Autorización/consentimiento específicos y protocolo | Investigador autorizado codificado; profesional solo campos acordados; admin técnico no | Según protocolo; suprimir/exportar cuando proceda | Alto con combinaciones, respuestas raras, edad/ubicación |
| Check-in y prácticas | Seguimiento de actividades/adherencia | Puede revelar estado sensible; limitar opciones | Finalidad específica y voluntariedad | Participante; profesional asignado solo si autorización; investigador codificado | Duración definida; agregar y eliminar individuales cuando permitido | Medio/alto por patrón temporal y respuestas raras |
| Conversación de FAQ | Dar respuesta durante sesión | Potencialmente sensible, puede incluir datos del menor | No persistir por defecto; tratamiento/transmisión requiere aviso/aprobación | En memoria de petición; proveedor LLM solo si aprobado | No guardar raw; logs técnicos sin contenido; política de retención del tercero verificada | Muy alto, narrativas suelen identificar aunque seudónimo |
| Metadatos FAQ: modo, fuentes, abstención, latencia | Calidad y uso sin contenido | Bajo/indirecto, pseudonimizar | Aviso de servicio/estudio según finalidad | Analytics con IDs técnicos no directos | Agregación por ventana aprobada; granularidad corta y expiración | Medio por timestamps, eventos raros |
| Señal de riesgo/solicitud humana | Atender necesidad y dejar constancia operativa mínima | Alta sensibilidad; no crear a partir de inferencia opaca | Consentimiento/obligación y protocolo de atención validados | Solo profesional/equipo asignado; seguridad mínima; no admin general | Política especial aprobada; retención legal/ética por definir | Muy alto; contenido narrativo potencialmente identificable |
| Preferencias canal, quiet hours, entrega | Comunicación solicitada | Contacto indirecto; opt-in por canal | Consentimiento opcional explícito y revocable | Notification service y proveedor de canal | Hasta revocación + estado mínimo de supresión necesario | Medio; enlazable al participante |
| Contenido visible y versiones | Entregar programa y citar fuentes | Sin dato personal | Licencia/autorización editorial | Editor/validador y todos los usuarios según publicación | Versión inmutable con archivo y retirada según licencia | Bajo, salvo fuente restringida |
| Auditoría administrativa | Seguridad y responsabilidad | Puede enlazar a usuario; sin payload | Finalidad de seguridad y política validada | Seguridad/auditor limitado | Plazo aprobado, acceso restringido, integridad protegida | Medio si actor/tiempo/object id permiten asociación |
| Export y artefacto | Análisis de investigación aprobado | Codificado, aún potencialmente personal | Protocolo/acceso explícito | Investigador autorizado; no proveedor genérico | URL/archivo temporal expirado; borrado; registro mínimo de descarga | Alto pese a codificación; revisar reidentificación |
| Foro/comunidad (futuro) | Interacción de pares | Narrativa sensible y contenido de terceros | Consentimiento específico y reglas/moderación | Moderadores con mínimo acceso; proveedor solo si aprobado | Política propia, reporte/borrado/retención definida | Muy alto; no incluir hasta gate |

**No recopilar por defecto:** nombre, número de documento, dirección, escuela, ubicación exacta, nombre del niño, fecha completa de nacimiento, foto, audio, diagnósticos, datos médicos, detalle de presunto abuso ni texto libre extenso. Si una variable es científicamente necesaria, documentar justificación, granularidad mínima, acceso, consentimiento y riesgo antes de instrumentarla.

## 4. Consentimiento, derechos y ciclo de vida

Mantener registro de finalidades/decisiones separado y no inferir que aceptar servicio equivale a aceptar investigación, datos sensibles, contacto profesional, correo, SMS, WhatsApp, foro o IA externa.

1. Mostrar aviso simple por capas (responsable, finalidades, datos, terceros, transferencias, retención, contacto y derechos) y versión completa accesible.
2. Recoger elecciones separadas para servicio, investigación, datos sensibles si aplican, contacto profesional y cada canal opcional, de manera no preseleccionada salvo revisión legal.
3. Guardar solo evidencia necesaria: versión, finalidad, estado, fecha/hora, canal/actor y referencia de texto; no copiar narrativa al log.
4. Para retiro, detener tratamiento futuro dependiente y tareas/canales; derivar qué se elimina, se conserva por deber aplicable o se anonimiza según protocolo y dictamen. No prometer supresión inmediata universal.
5. Para acceso/consulta, entregar explicación y datos autorizados sin revelar terceros; para corrección, preservar rastro mínimo; para supresión, aplicar a DB, storage, exportaciones y proveedores según capacidad/contrato; registrar resultado y excepciones.
6. Definir plazos internos, verificación proporcional de identidad, contacto institucional y responsable; reflejar solicitudes en un registro segregado.
7. Respaldos: documentar ventana de expiración de copias y procedimiento de borrado/retención legal; restauración debe reaplicar tombstones/bajas antes de exponer datos.

## 5. Arquitectura conversacional e IA responsable

### Modos separados (no todos para MVP)

| Modo | Recomendación inicial | Política y control |
|---|---|---|
| Guía de situación inmediata | MVP solo si existe aprobación profesional y operación de seguridad | Árbol determinista y copy/ramas preaprobados; opciones accesibles; no generación libre; botones humanos/emergencia validados y limitaciones explícitas. |
| Compañero/FAQ de sesión | MVP limitado | RAG con corpus cerrado, fuente/versión aprobada, respuesta grounded o extractiva; filtro/umbral evaluado; abstención; sin conversación persistente por defecto. |
| Check-in motivacional breve | Posible MVP si copy revisado | Plantillas y reglas, baja inferencia, no respuesta a crisis; consentimientos/preferencias. |
| Simulador de escenarios | Posterior | Contenido ficticio aprobado, no recomendación personalizada; evaluación con profesionales/usuarios y riesgo. |
| Role-play IA/avatar | Fuera de MVP | Gate de evaluación ética, presupuesto, seguridad de menores, transparencia, límites, moderación y estudio. |

### RAG cerrado para FAQ

- **Fuentes:** únicamente documentos oficiales entregados/autorizados y versiones aprobadas por editor/validador. Verificar derechos/licencias, vigencia, traducción, correspondencia con el programa y revisión profesional; los PDFs del repositorio no fueron leídos en esta auditoría.
- **Registro:** `document_id`, título, versión, owner, autoridad/fuente, checksum, licencia, aprobado por, fecha, vigencia/retiro, ubicación del pasaje, extractor, chunk config y embedding version.
- **Recuperación:** búsqueda con metadatos/versiones y filtros de cohorte/sesión si aplica; umbrales se calibran en dataset local y revisión experta. No heredar historial controlado por cliente; si hay seguimiento, backend mantiene mínimo contexto de sesión no identificado y lo redacta antes del modelo.
- **Umbral:** no reutilizar `0.65` como garantía. Definir rango por evaluación de precisión/recall y abstención; calibrar por tema crítico. Ante baja evidencia, conflicto documental o fuente no aprobada, abstener. Umbral/versión registradas en metadatos técnicos sin consulta raw.
- **Generación:** por defecto apagada inicialmente. Si se aprueba, proveedor/modelo/retención/región/uso para entrenamiento/subprocesadores se revisan; contenido enviado se minimiza, sin identificadores; estructura de mensajes separa instrucciones confiables de documentos no confiables. El modelo no decide permisos, consentimiento ni alertas.
- **Citas y verificación:** respuesta debe referir pasajes que existan en índice vigente. Validador comprueba esquema, número/tamaño de estrategias, límites y cita; prueba semántica por evaluación independiente. Si no puede justificar un enunciado, se omite/abstiene.
- **Evaluación:** conjunto versionado de preguntas esperadas, OOD, sensibles/seguridad, consultas ambiguas, ataques de prompt injection, errores de parser y documentos conflictivos. Medir precisión de citas, soporte factual con revisión humana, abstención OOD, utilidad, latencia y diferencias por subgrupo cuando éticamente proceda. Ragas es señal auxiliar, no certificación.

### Prompt injection, salida y protección de datos

1. Tratar pregunta, historial y texto recuperado como **datos no confiables**. Los delimitadores textuales no son frontera de seguridad.
2. No concatenar turnos `assistant` del cliente como verdad; preferir request stateless o historial firmado/servidor mínimo con TTL. No permitir que prompt solicite otros datos o herramientas.
3. Sin herramientas con permisos/escritura, sin navegación web ni acceso arbitrario a archivos. El asistente no debe consultar datos de participantes.
4. Corpus solo editorialmente confiable; escaneo y revisión de instrucciones incrustadas; salida filtrada por política; renderizado como texto escapado (la UI actual usa `textContent`, una práctica a preservar).
5. Redactar identificadores obvios antes de transmisión, pero no asumir que detección de PII resuelve anonimización. Preferencia: proveedor LLM desactivado hasta aprobación y prueba de no envío.
6. Registrar solo `request_id`, modo, versión de prompt/corpus/modelo, latencia, outcome y códigos de error; no texto/prompt/respuesta completa por defecto.
7. Timeout, proveedor caído, JSON inválido, recuperación vacía o discrepancia de citas → respuesta de indisponibilidad/abstención segura; no reintentos ilimitados ni fallback a modelo de alcance abierto.
8. Tests de seguridad nunca usan datos reales; corpus sintético de ataques; red-team y revisión de falsos positivos/negativos previo a liberar.

### Señales y escalamiento humano

- Detección de palabras puede priorizar revisión, **no diagnóstico ni clasificación definitiva**. Falsos positivos pueden asustar/interrumpir; falsos negativos pueden dejar sin apoyo. Cualquier cambio de umbral/guion necesita revisión profesional y evaluación documentada.
- El texto para usuarios debe diferenciar: emergencia inmediata, solicitud de contacto no urgente y plataforma sin cobertura. No afirmar que alguien lee mensajes en tiempo real si no es verdad.
- Definir dueños de guardia, calendario, zona horaria, horas y plazo objetivo, canal, acuse, procedimiento fuera de horario, reemplazo de contacto y cómo manejar no respuesta. Si no hay capacidad humana garantizada, no presentar el sistema como monitor de crisis.
- Revisar oficialmente las líneas/recursos presentados, su alcance, disponibilidad y actualización; el código actual menciona ICBF 141 y emergencia 123, pero no se verificaron en este trabajo.
- Señal/solicitud debe tener acceso por asignación, auditoría, estado y resolución mínima; análisis de falsas señales y métricas de tiempo solo con autorización, sin usarlo como métrica de desempeño individual del psicólogo.

## 6. Modelo de amenazas resumido (STRIDE + riesgo humano)

Escala cualitativa antes de controles: Probabilidad P (Baja/Media/Alta), Impacto I (Medio/Alto/Crítico). No es un pentest; controles propuestos deben validarse.

| ID | Amenaza/activo | P/I | Controles preventivos/detectivos | Prueba/evidencia requerida |
|---|---|---|---|---|
| R-01 | BOLA/IDOR: cambiar participante/cohorte en API y acceder a datos ajenos | Media/Crítico | Deny-by-default, autorización por relación, scopes, IDs aleatorios, consultas filtradas en DB | Matriz negativa para todos los recursos, pruebas con cuentas A/B |
| R-02 | Elevación de privilegios o admin monolítico | Media/Crítico | Grants separados y temporales, separación de funciones, MFA/reauth para export/acciones críticas, audit | Tests rol/alcance, revisión de grants y logs |
| R-03 | Exposición de conversación/datos del menor al LLM o logs | Media/Crítico | No persistencia por defecto, proveedor apagado, payload mínimo, DPA/transferencia aprobada, logging redacted | Captura de requests/logs sintéticos y control de egress |
| R-04 | Prompt injection en input/corpus recuperado | Alta/Alta | Trust separation, corpus aprobado, no tools, output policy, límites/abstención | Red-team corpus de ataques con métricas y revisión |
| R-05 | RAG recupera fuente obsoleta o fuera de alcance | Media/Alta | Versionado, filtros de aprobación/vigencia, rollback, umbral evaluado, citas | Pruebas de retirada/conflicto/OOD y suite dorada |
| R-06 | Falso negativo/positivo de riesgo | Media/Crítico | Guiones aprobados, señal no diagnóstica, alternativa humana/urgencia, horarios claros, revisión de patrones | Casos sintéticos adversariales y validación profesional; no desplegar sin responsable |
| R-07 | Abuso/DoS/costo del endpoint público | Alta/Alta | Auth, rate limits por cuenta/IP, cuotas y presupuesto proveedor, timeouts/circuit breaker | Load/security test y alertas de cuota |
| R-08 | Robo de cuenta/session fixation/CSRF | Media/Alta | Hash robusto, cookies seguras, CSRF, rotación y expiración de sesión, recuperación segura, throttling | Tests de sesión/csrf, revisión de configuración TLS/cookies |
| R-09 | XSS o contenido cargado activo en respuestas | Media/Alta | Escapar salida, CSP, no HTML rico sin sanitizador, mantener `textContent` | Tests de payloads en todas vistas y cabeceras |
| R-10 | Reidentificación de cohorte pequeña/export | Media/Alta | Supresión k, generalización de fechas, minimización, revisión de unicidad, expiración del artefacto | Pruebas de combinaciones/filtros; aprobación del investigador |
| R-11 | Compromiso de proveedor/dependencia/secretos | Media/Alta | pin/lock, scan de dependencias, rotación, secrets manager, least privilege, contrato | SBOM/scan/ciclo de rotación y simulación de incidente |
| R-12 | Pérdida/corrupción de base de datos o despliegue efímero | Media/Alta | DB/disco durable, backup cifrado, restauraciones, RPO/RTO aprobados | Restore game day con acta y medición |
| R-13 | Abuso del foro/daño entre pares (futuro) | Media/Crítico | No activar; moderación, reportes, SLA, reglas y protección | Gate de aprobación y simulacro de moderación |
| R-14 | Publicar guía errónea/no licenciada/desactualizada | Media/Crítico | Revisión profesional/legal, licencia, doble aprobación, expiración/rollback | Tests de publicación, muestreo de contenido y bitácora |

## 7. Registro de decisiones pendientes de privacidad

Responsable de tratamiento/encargado; base jurídica y finalidades; transferencias internacionales; consentimiento del cuidador/representación/asentimiento; clasificación 8430; tratamiento de datos sensibles/relatos; necesidad de autorización de proveedor; plazos de conservación y archivo; respuesta de derechos; reglas de anonimización/supresión de celdas; manejo de incidentes y notificación; política de grabaciones; si se permite proveedor IA; texto de aviso y encargado institucional. Todas requieren decisión institucional documentada antes de piloto.


## Referencias oficiales para revisión institucional

Consulta realizada el 2026-10-17. Son puntos de partida para asesoría institucional, no una conclusión de cumplimiento. **Advertencia sobre investigación en salud:** el Ministerio de Salud publicó en 2026 un proyecto para segunda consulta pública que propone derogar parcialmente la Resolución 8430 de 1993; el plazo publicado para comentarios fue del 29 de julio al 4 de agosto de 2026. En la búsqueda realizada no pude confirmar la promulgación de un texto definitivo. Antes del piloto, la institución debe verificar el estado normativo final en el Ministerio y no asumir ni que el borrador ya rige ni que la Resolución 8430 continúa sin cambios. [Listado oficial de proyectos del Ministerio](https://www.minsalud.gov.co/Normativa/proyactadm/Forms/Proyectos.aspx) · [Borrador de resolución 2026](https://www.minsalud.gov.co/Normatividad_Nuevo/PR%20Investigaci%C3%B3n%20en%20Salud%20con%20Seres%20Humanos.pdf).

- [Ley 1581 de 2012 — Gestor Normativo de Función Pública](https://www.funcionpublica.gov.co/eva/gestornormativo/norma.php?i=49981).
- [Decreto 1074 de 2015 — texto publicado en Diario Oficial/Rama Judicial](https://sidn.ramajudicial.gov.co/SIDN/NORMATIVA/TEXTOS_COMPLETOS/5_DECRETOS/DECRETOS%202015/Decreto%201074%20de%202015%20%28Decreto%20%C3%9Anico%20Reglamentario%20del%20Sector%20Comercio%29.pdf).
- [Ley 1098 de 2006 — Gestor Normativo de Función Pública](https://www.funcionpublica.gov.co/eva/gestornormativo/norma.php?i=22106).
- [Ley 1090 de 2006 — Gestor Normativo de Función Pública](https://www.funcionpublica.gov.co/eva/gestornormativo/norma.php?i=66205).
- [Resolución 8430 de 1993 — Ministerio de Salud](https://www.minsalud.gov.co/sites/rid/lists/bibliotecadigital/ride/de/dij/resolucion-8430-de-1993.pdf).
- [WCAG 2.2 — Recomendación W3C](https://www.w3.org/TR/WCAG22/).

Antes del piloto, la institución debe confirmar vigencia, normas complementarias, interpretación aplicable, clasificación de riesgo de investigación, rol de responsable/encargado, textos de autorización, transferencias, derechos, retención y procedimiento institucional. La conformidad WCAG tampoco se presume por usar HTML semántico: necesita evaluación técnica y manual.
