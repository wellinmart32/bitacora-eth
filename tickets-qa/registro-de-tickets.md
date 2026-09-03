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

**Estado:** Ready for Release (reasignado a Marcin para fase de
release)

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

**[ACTUALIZADO — 11 ago 2026]:** se activó el flag "orders_ui_v2"
primero en dev, pero las órdenes de prueba ahí fallaban el Clinical
Review por falta de guidelines cargados en ese ambiente (no
relacionado al ticket — ver nuevo aprendizaje en
reglas-y-aprendizajes-de-testing.md). Marcin confirmó que el flag
también funciona en producción y que el matching de guidelines está
fuera de alcance de este ticket.

**Validación completada en Medpoint test org (producción):**
- AC1: orden ya completada/submitted (Test TT303) → aterrizó directo
  en la página de resumen completo, sin paso intermedio.
- AC2: orden en progreso (Cory Runge, draft Step 2/4) → aterrizó
  exactamente en ese paso al usar "Resume Order".

Ambos ACs confirmados con evidencia (3 capturas) en comentario de
Jira.

**Pendiente aparte (no bloquea este ticket):** ajuste visual fino
contra Figma (iconos en badges CPT, ubicación de Download Section,
control chevron, etc.), ya identificado por Marcin.

**Cierre:** Ready for Release. Reasignado a Marcin (ingeniero
original) según protocolo del equipo confirmado en daily de hoy:
tickets ready-for-release se asignan al ingeniero original.

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
**Estado:** Ready for Release

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

**[ACTUALIZADO — daily standup, 06 ago 2026]:** Marcin confirmó que
ordenar por "momento de inserción a la cola" (no por createdAt real)
es el comportamiento INTENCIONAL, tal como él entendió la tarea
originalmente. Su razonamiento: ordenar por createdAt real podría
mezclar trazas ya anotadas con no anotadas si se agregan trazas
viejas después de que la cola ya tiene trazas más nuevas anotadas.
Pendiente llevar esto a conversación con Taru para confirmar si se
mantiene así o se ajusta.

**Cierre — 11 ago 2026:** Taru confirmó por Slack que el
comportamiento coincide con lo que ella tenía en mente. Ticket
cerrado sin cambios adicionales. Estado: Ready for Release.

---

## TT-345 — Failed Determinations for CR Cases

**Link:** https://ethermed.atlassian.net/browse/TT-345
**Estado:** In Progress (reasignado a Van Damrongsri)

**Qué pedía:** investigar por qué órdenes que antes generaban
determinación clínica dejaron de hacerlo. Afecta a MPM, Careflow y
otros clientes. Alta prioridad (Uzo), ligado a evaluación final con
Tanner Health System.

**Hallazgo:** el síntoma agrupaba 3 causas raíz distintas:

1. SNF Admission (Hayden Righter, Medpoint test org) - ya corregido
   por ingeniería antes de retomar el ticket. Verificado: genera
   determinación completa.
2. Christopher Still (Tanner Health System Prod, CPT 52310, Anthem
   GA Commercial) - "No clinical guideline found". Confirmado en
   Backoffice de producción (sección Guidelines, filtro CPT 52310):
   no existe ningún guideline para ese código. No es bug de
   matching, es gap de librería.
3. CareFlow Prod / integración Availity - al menos 7 órdenes en 2
   meses fallan con "Service review not found" tras generar
   determinación exitosa. Verificado en 2 casos (Woodrow Roberts -
   Humana, Stefano Tarantolo - Aetna): mismo patrón con payers
   distintos, ambos SNF Admission. Sugiere problema en cómo CareFlow
   Prod envía las solicitudes SNF Admission a Availity, no
   específico de un payer.

**Evidencia:** comentario publicado en Jira con 8 capturas
documentando los 3 casos.

**[ACTUALIZADO — 18 ago 2026]:** Taru Jain confirmó por escrito
(comentario Jira) que los casos 2 y 3 son issues separados que se
están resolviendo en tickets aparte. Taru no mencionó números de
ticket específicos, solo indicó genéricamente "being solved in
separate tickets". El scope de TT-345 queda acotado a "failed
determination view".

**[ACTUALIZADO — Sprint Planning, 19 ago 2026]:** Van pidió
formalmente dividir TT-345 en tickets separados por no estar
relacionados al bug original; Dominick aprobó. Taru quedó encargada
de etiquetar/crear los tickets correspondientes a los 2 hallazgos.
Sprint actualizado por Dom Garbellano: de Apollo 34/35/36 pasa a
Apollo 34/35/36/37 (sigue activo, extendido).

**[CORRECCIÓN — 03 sep 2026]:** se investigó a fondo (Slack con
Taru, comentario real de Jira en TT-345, descripciones de TT-349 y
TT-350, búsqueda en todo Jira por la frase exacta "Service review
not found") y se confirmó que TT-349 (member_id overwrite) y TT-350
(nursing home status) son bugs completamente distintos, sin
relación con el caso 2 (library gap CPT 52310) ni con el caso 3
(Availity "Service review not found"). La referencia previa a
TT-349/TT-350 en esta ficha era incorrecta.

**Cierre:** caso 1 resuelto. Caso 2 cerrado como TT-430, estado
"Won't Do" (Jim Koger confirmó que no hay gap de librería). Caso 3
cerrado como TT-431, estado "Won't Do", citando que se resuelve
externamente con Availity (según lo mencionado por Dom), sin
vincular ningún ticket de Jira adicional.

**Nota — TT-345 (ticket padre):** el scope quedó acotado a "failed
determination view" (Caso 1, ya resuelto), pero el ticket sigue
**In Progress**, reasignado a Van Damrongsri. Pendiente confirmación
de Dom Garbellano (pregunta enviada, sin respuesta aún) antes de
poder mover el estado del ticket.

---

## TT-351 — Capture right answer from the annotator in AI Workbench

**Estado:** Finalizada (confirmado por Dom Garbellano, 18 ago 2026,
cambio de estado Ready for Release → Finalizada)

**Qué pedía:** verificar 2 Acceptance Criteria del flujo de
anotación en AI Workbench:
- AC1: si la respuesta del LLM se marca correcta, la UI no debe
  cambiar (no deben aparecer campos extra).
- AC2: si se marca incorrecta, debe aparecer la sección "Corrected
  Answer" con las opciones correctas (True/Unknown/False) y un
  campo de razonamiento.

**Cómo se probó:** se probaron ambos criterios con múltiples
preguntas en el flujo de anotación. AC1 probado con respuesta
AI-proposed "Unknown" y también "Yes", mismo resultado correcto en
ambos casos. AC2 probado marcando una pregunta como incorrecta,
confirmando que aparece el formulario de corrección esperado, y
probado también en otra sección/pregunta distinta con el mismo
comportamiento consistente.

**Caso edge probado:** cambiar una respuesta de incorrecta a
correcta limpia el formulario de "Corrected Answer" sin dejar
rastros/estado residual.

**Limitación encontrada:** se intentó probar en un segundo caso de
las colas de anotación para verificar consistencia, pero varios
traces no se pudieron anotar ("This trace can't be annotated") o
ya estaban bloqueados/completados sin campos editables. Dado que
los resultados fueron consistentes en todas las preguntas que sí
se pudieron probar, se cerró la validación con ese alcance.

**Nota de documentación (no bug):** la descripción original del
ticket decía que las opciones eran "Yes/No/Unknown", pero la
implementación real usa "True/Unknown/False" (coincide con una
captura de Kevin en los comentarios). Se sugiere actualizar la
descripción del ticket para mayor claridad.

**Evidencia:** 6 capturas publicadas en el comentario de Jira.