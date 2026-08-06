# Registro de tickets QA

Ficha por cada ticket trabajado. Formato: qué pedía, qué se
encontró, historial si los criterios cambiaron, estado final.

---

## TT-254 — Live Extraction Judges — Online Trace Evaluation

**Link:** https://ethermed.atlassian.net/browse/TT-254
**Estado:** Done

**Qué pedía:** renombrar los spans de extracción en Langfuse a
extract_codes, extract_markdown, extract_flow, para poder
diferenciar cada dimensión y que cada judge apunte a la suya.

**Hallazgo original (QA, 24 jul 2026):** AC #1 no cumplido
literalmente — los spans no fueron renombrados en código. En su
lugar se agregó un campo metadata.task. AC #2, #3, #4 sí cumplidos
(3 evaluadores activos, exactamente 3 scores por trace, visibles en
trace y guideline page).

**[ACLARADO — Marcin Talik, 04 ago 2026]:** el rename nunca fue
necesario para el scoring (los judges siempre filtraron por
metadata.task, no por nombre de span). No se hizo por 2 razones
técnicas: rompe la agregación de métricas de todas las llamadas
LLM del sistema, y va contra la convención OpenTelemetry (el
propósito va en atributos, no en el nombre). La identificación por
dimensión funciona vía metadata tag. El filtrado por dimensión en
la UI de Langfuse se movió a tarea de seguimiento (vía prompt link
gestionado).

**Impacto en criterios:** 3 de 5 cumplidos como estaban escritos.
AC #1 cambia: se logra vía metadata tag en vez de rename literal.
AC #5 (split codes judge en CPT/HCPCS/ICD-10) se descartó antes por
decisión — un score combinado cubre los tres.

**Cierre:** Uzo confirmó el 04 ago 2026: "I've read through his
comments, and noted. We can mark the ticket as done."

---

## TT-257 — Extraction Review Human Feedback buttons

**Estado:** Cerrado / Finalizado (confirmado por Uzo)

**Qué pedía:** verificar que los botones de feedback humano en la
revisión de extracción funcionen correctamente.

**Hallazgo:** los botones usan LiveView sobre WebSocket, no HTTP,
por eso el Network tab aparecía vacío (comportamiento esperado, no
bug). El problema real era ambiental: almacenamiento de ClickHouse
en dev lleno desde el 17 de julio, causando que los scores no se
guardaran silenciosamente.

**AC:** AC#1 y AC#2 pasan en instancias de Langfuse funcionando.
AC#3 (scores separados por revisor) falla por diseño, decisión de
dejar para después.

**Cierre:** Uzo cambió el estado a Finalizada (confirmado por Ronny
el 04 ago 2026).

---

## TT-334 — Remove duplicated per-guideline clinical summaries

**Estado:** Completado y documentado

**Qué pedía:** que al detectar varios guidelines en una orden, se
muestre un solo resumen clínico general (no uno repetido por
guideline).

**Cómo se probó:** se buscó una orden con múltiples guidelines
detectados simultáneamente. Uzo indicó la organización "Medpoint
test org" (ethermed.online, confirmada como org de prueba). Se creó
una orden nueva con CPT 73721 + ICD-10 M25.561 (dispara 5
guidelines). Se verificó en el Activity Timeline: "generating
clinical summary" aparece UNA vez, "generating determination
summary" aparece 5 veces (una por guideline). Confirmación visual:
un solo resumen clínico en Determination y Submit, sin duplicados.

**Evidencia:** 6 capturas publicadas en el comentario del ticket.

---

## TT-326 — Order summary page navigation for submitted orders

**Estado:** Listo para QA, pendiente activar feature flag

**Qué pedía:** verificar navegación a la página de resumen para
órdenes ya enviadas (submitted).

**Bloqueo original:** no se encontraron órdenes con status
"Submitted" en las organizaciones de prueba revisadas (QA Smoke
Test Org, Pharmerica, Tanner Test). Marcin tenía fixes locales
relacionados sin desplegar aún.

**[ACTUALIZADO — confirmado en daily standup, 06 ago 2026]:** Kevin
confirmó que el bloqueo se resolvió (fixes de Marcin desplegados).
El desarrollo está listo para QA — falta activar el feature flag
"orders_ui_v2" (segunda versión del layout de orden) en el ambiente
donde se pruebe (dev o producción).

**Alcance de la prueba (aclarado por el equipo):** solo debo probar
lo que ya coincide con el Figma actual, no trabajo adicional que se
vaya a agregar después.

**Bloqueante técnico actual:** no encontré dónde se activa el flag
"orders_ui_v2" en el Backoffice (revisado: Edit org, System
Settings > Platform Settings completo, User Management). Escalado
a Marcin por Slack, respuesta pendiente.

**Próximo paso:** esperar respuesta de Marcin sobre dónde activar
el flag, luego validar.

---

## TT-305 — Humana J15 ICD-10 extraction fix

**Estado:** Cerrado

**Qué pedía:** corregir el bug donde el código de jurisdicción
Medicare "J15" se confundía con el código ICD-10 J15 (neumonía).

**Verificación:** las 6 filas afectadas (11.1, 13.1, 14.1, 15.1,
47.1, 48.1) confirmadas pasando tras reprocesamiento. Nota técnica:
el Backoffice apila cada reprocesamiento como un registro nuevo, no
sobreescribe — hay que forzar un nuevo run para ver la corrección.

---

## TT-303 — Chronological ordering of newly added cases in queue

**Link:** https://ethermed.atlassian.net/browse/TT-303
**Estado:** En progreso — comentario con hallazgos publicado

**Qué pedía:** que al agregar un caso nuevo a la cola de anotación
humana (Human Annotation queue), aparezca al final ordenado
cronológicamente por createdAt (más viejo a más nuevo).

**Contexto del ticket:** Marcin dejó pregunta abierta sobre si
mostrar reviews fallidos (sin determinación) en la cola — sin
resolver por el equipo.

**Cómo se probó:**
1. Se exploraron las 2 colas existentes en Langfuse dev (Kevin's
   Test Annotation Queue, Test Annotation Queue).
2. Se confirmó que la UI no tiene columna "Created At", solo
   "Completed At".
3. Se intentó crear una orden de prueba en Medpoint test org
   (producción) — se procesó pero NO generó ningún trace en
   Langfuse dev. Se le preguntó a Marcin cómo se agrega un caso a
   una cola; confirmó que es manual (botón Annotate sobre un
   trace), no automático.
4. Se creó una segunda orden de prueba en QA Smoke Test Org
   (ambiente Dev) — esta sí generó traces visibles.
5. Se agregó manualmente el trace nuevo a Kevin's Test Annotation
   Queue: apareció al final (AC #1 en apariencia cumplido).
6. Para probar si el orden depende de createdAt real, se agregó un
   tercer trace de un día antes (más viejo cronológicamente).
7. Resultado: el trace más viejo apareció AL FINAL de la cola,
   después del más nuevo — confirmando que el sistema ordena por
   "momento de inserción a la cola", no por createdAt real.

**Conclusión:** AC #1 se observa cumplido pero no por la razón
esperada. AC #2 NO se cumple: createdAt no es lo que determina el
orden.

**Evidencia:** 4 capturas (Column Visibility, trace de hoy, trace
de ayer, cola final) subidas al comentario del ticket.

**Próximo paso:** esperar respuesta del equipo sobre la implicación
de este hallazgo, y sobre la pregunta pendiente de Marcin (reviews
fallidos en la cola).