# Reglas y aprendizajes de testing de tickets QA

Aprendizajes generales sobre cómo probar tickets técnicos (no
validación de payers del Excel). Reutilizables entre tickets
distintos.

---

## Entornos: Dev vs Producción

- **Dev:** ethermed-dev.ethermed.run (Backoffice), y también existe
  una app de órdenes en ese mismo dominio
  (ethermed-dev.ethermed.run/orgs/.../orders).
- **Producción:** ethermed.online. Contiene organizaciones de
  prueba autorizadas (ej. "Medpoint test org", confirmada por Uzo)
  donde SÍ se puede trabajar con datos ficticios, aunque el dominio
  sea de producción.
- **Importante (confirmado 04 ago 2026):** órdenes creadas en
  Medpoint test org (producción) NO generan trazas visibles en la
  instancia de Langfuse dev (langfuse-dev.ethermed.run). Las
  órdenes creadas en organizaciones de Dev (ej. QA Smoke Test Org)
  SÍ generan trazas ahí. Si se necesita ver el trace de una orden
  en Langfuse dev, crear la orden de prueba en un ambiente Dev, no
  en producción.
- **Importante (confirmado 11 ago 2026, TT-326):** el ambiente Dev
  (QA Smoke Test Org, y aparentemente otras orgs de prueba en dev)
  no tiene cargada la misma librería de guidelines que producción.
  Al crear órdenes de prueba ahí con códigos CPT/ICD-10 que sí
  funcionan en producción (ej. CPT 73721 + M25.561), el Clinical
  Review falla con "No clinical guideline found with the provided
  data" y la orden queda en estado error, sin llegar a completarse.
  Para pruebas que necesiten una orden realmente
  completada/submitted, usar Medpoint test org en producción en vez
  de orgs de prueba en dev.

---

## Cómo agregar un trace a una cola de Human Annotation

(Confirmado por Marcin Talik, 04 ago 2026)

No es automático. Pasos:
1. Ir al detalle del trace en Langfuse (Tracing > clic en la fila)
2. Click en el botón "Annotate" (junto a "Add to datasets")
3. Click en la flechita ▼ junto a "Annotate"
4. Se despliega "In queue(s)" con las colas disponibles
5. Seleccionar la cola deseada

Cita de Marcin: "you need to add it in langfuse and then it will
show up in queue in ai workbench... I assume you wanna make an
order and then add the trace that is generated with the order to
queue"

---

## Organizaciones de prueba conocidas

- **Medpoint test org** (producción, ethermed.online) — confirmada
  por Uzo como org de prueba con casos de múltiples guidelines.
  Org ID: 019ac522-6ddc-7d35-95e5-b69e3176c063
- **QA Smoke Test Org** (Dev, ethermed-dev.ethermed.run) — llena de
  órdenes automáticas de smoke test (nombres "Unknown Patient",
  MRN tipo SMOKE-...). Sirve para crear órdenes manuales de prueba
  también. Org ID: 019edca1-db08-75e9-9567-ba4ab6243a31
- **Pharmerica, Tanner Test, BB Test Provider, MT test
  Organization** — otras orgs de prueba en Dev, revisadas para
  TT-326 sin encontrar órdenes "Submitted".

CPT/ICD-10 combo que dispara 5 guidelines simultáneos (útil para
pruebas de múltiples guidelines): CPT 73721 + ICD-10 M25.561.

---

## Verificar mecanismos no documentados

Cuando un ticket depende de un mecanismo interno que no es visible
desde la UI (ej. "qué dispara que un caso entre a una cola"), no
asumir — probar empíricamente creando casos de control y comparando
resultados. Ejemplo: para TT-303 se probó agregando un trace viejo
después de uno nuevo, para verificar si el orden de la cola
depende de createdAt real o de orden de inserción — reveló que es
lo segundo.

---

## Escalar preguntas técnicas

Igual que con Ben/Uzo en validación de payers, evitar preguntar
frecuentemente y esperar sesiones en vivo si se puede resolver por
texto. Si alguien ofrece una sesión conjunta ("we can go through
that together"), responder confirmando que se puede resolver por
cuenta propia, para no comprometer tiempo de alguien con agenda
ocupada.