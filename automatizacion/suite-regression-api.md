# Suite de regression testing de API

## Origen

Dom pidió pivotar de tickets Apollo a trabajar con Oscar (equipo
Hopper) en automatización de pruebas de API, vía Slack.

## Documentación de referencia de Oscar (Confluence)

- "API Regression Testing – Coverage Matrix"
  (https://ethermed.atlassian.net/wiki/x/AYCDNQ) — matriz de 41
  escenarios en 7 áreas (Auth, Create/Get/Update/Submit Order,
  Documents, Clinical Reviews)
- "API Smoke Testing – Current State and Coverage" — 11 escenarios
  happy-path, corre contra Dev API, gate de release vía GitHub
  Actions

## Historial del PR

- PR original de Oscar (#991, rama qa/api-regression-v1): 14 tests.
  Rechazado por Kevin — 8 duplicaban tests internos exactos
  (CREATE-01, GET-03, UPDATE-02, DOC-01, DOC-02, SUBMIT-01,
  REVIEW-01, REVIEW-02).
- Se continuó el trabajo agregando 26 tests nuevos, confirmando
  cada comportamiento contra el API real de Dev con curl antes de
  escribir cada test.
- Revisión cruzada final contra order_controller_test.exs y
  clinical_review_controller_test.exs encontró 5 duplicados
  adicionales entre los nuevos (CREATE-08, GET-04, DOC-04, DOC-07,
  REVIEW-06) — también removidos.
- PR final: rama qa/api-regression-v2, 27 tests, creado por
  Alexander Martinez (el PR de Oscar nunca se fusionó a main).

## Hallazgos técnicos confirmados contra el API real
(no documentados en ningún otro lado)

- POST /order con {} se acepta y crea draft vacío (201) —
  comportamiento intencional, no bug.
- GET a UUID inexistente o malformado devuelve 204 en vez de
  404/422 — discrepancia real, ya en tickets separados.
- Actualizar o reenviar (submit) una orden ya submitted SIEMPRE
  crea una nueva versión y reprocesa desde cero, incluso sin
  cambios reales.
- Campos como :type y código CPT no tienen validación en backend
  (aceptan cualquier string).
- El campo "id" de una orden está protegido (se ignora si se
  intenta sobrescribir), pero el campo "status" NO — se puede
  forzar directamente vía PUT sin ningún control. Reportado a
  Oscar como hallazgo a escalar con Dom/Kevin.
- Documento vacío o con MIME mismatch se rechaza con 400; archivo
  que excede el límite de 25MB devuelve 400 o 413 de forma
  inconsistente (se ajustó el test para tolerar ambos).
- Múltiples service_codes en una orden generan un clinical review
  independiente por cada código.

## Estado final

27 tests, 25 pasando, 2 fallos intencionales (GET-01, GET-02 —
mismo desfase 204 vs 404/422 ya documentado, mantenido a propósito
porque detecta la deriva real del sistema).

## Documentación de Confluence actualizada en paralelo

La página "API Regression Testing – Coverage Matrix" fue editada
marcando cada escenario original como CUBIERTO, DUPLICADO,
DESCARTADO o PENDIENTE, y la sección V1 Scope se actualizó de "14
tests, 2 pendientes de confirmar" a "27 tests, 25 passing, 2 known
gap".

## Detalles técnicos para retomar la suite

- Enfoque: tests externos con Req contra el API desplegado en Dev
  (no ConnCase/in-process), para ejercitar auth/config/infra reales.
- Ubicación: apps/ethermed_web/test/external_regression/
  (orders_regression_test.exs, documents_regression_test.exs,
  clinical_reviews_regression_test.exs).
- Helpers en EthermedWeb.ExternalRegressionCase:
  create_regression_order!, get_json!, post_json_with_token!,
  upload_document!, upload_document_with_token!, valid_document_path,
  assert_status! (muestra status esperado, real y body al fallar),
  assert_eventually (polling para comportamiento asíncrono).
- Tag: @moduletag :external_regression, async: false.
- Cómo correr:
  mix test <ruta_del_archivo> --only external_regression
- Submit siempre responde 200 de inmediato; los errores aparecen
  después en el procesamiento. Los tests de clinical reviews usan
  assert_eventually (30s timeout, 1s intervalo).
- DOC-06 (archivo >25MB) devolvió 400 y 413 en corridas distintas;
  probablemente un proxy intercepta a veces. El test acepta ambos.