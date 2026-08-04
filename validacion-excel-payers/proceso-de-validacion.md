# Proceso de validación paso a paso

(Versión vigente — ver reglas-confirmadas-historial.md para el
historial de cómo evolucionó)

PASO 1 - Abrir el Google Sheet Testing Log y ubicar una fila con
status Extracted que tenga links disponibles.

PASO 2 - Abrir el link del backoffice de Ethermed desde esa fila.
(Entorno actual: producción, ethermed.online/backoffice — ver
terminologia-y-payers.md).

PASO 3 - Expandir todas las columnas del backoffice antes de
comenzar la verificación.

PASO 4 - Identificar el campo Title y leer el breadcrumb completo
para saber exactamente qué subsección del documento original
corresponde a ese guideline.

PASO 5 - Identificar el campo URL con el link al documento original
del payer y abrirlo en una pestaña nueva del navegador.

PASO 6 - Ubicar en el documento original la subsección específica
que indica el breadcrumb. Toda la validación se realiza únicamente
contra esa subsección.

PASO 7 - Verificar códigos:
- Determinar si la subsección tiene su propia subtabla en la tabla
  de códigos del documento original. Si NO la tiene, aplicar la
  regla de tablas con bloque general (ver
  reglas-confirmadas-historial.md).
- Para documentos de proveedores externos (Carelon, Evicore),
  aplicar las reglas específicas de su estructura, incluyendo
  evaluación de ICD-10 a nivel de header clínico.
- Incluir solo códigos en subcategorías que sugieran cobertura
  ("covered if selection criteria are met", etc.).
- Excluir códigos en subcategorías que sugieran no cobertura
  ("Other codes related", "Not covered", "Experimental",
  "Investigational", "Unproven", etc.).
- Códigos con "Not Covered if used to report any procedure outlined
  in Coverage Limitations section" SÍ se extraen; el árbol debe
  incluir nodos de Coverage Limitations para estos casos.
- Excepción: si la condición describe el alcance entero del
  guideline, equivale a Not Covered absoluto. NO extraer.
- Aplicar regla de versión del documento: validar contra la
  versión que el sistema usó al extraer, no la más reciente.
- Si hay bugs, empezar verificación manual por esos códigos primero.
- Si se confirma bug en una categoría, no es necesario seguir
  peinando el resto de esa categoría.
- Categoría con menos de 5 códigos → verificar todos.
- Categoría con más códigos → spot-check de 5 a 10.
- Categoría sin códigos extraídos → se omite.
- Códigos en rango con guion: verificar que estén dentro del rango
  Y que tengan el mismo número de dígitos que el rango original.

PASO 8 - Verificar Markdown:
- Debe contener el contenido completo de la subsección específica
  del breadcrumb, no un resumen.
- Description, Clinical Background y apéndices son aceptables si no
  generan ruido en el decision tree.
- Notas administrativas y referencias a otros documentos son
  aceptables si no influyen en el decision tree.
- Es bug si genera nodos incorrectos o innecesarios en el árbol.

PASO 9 - Verificar Decision Tree:
- Si no se ve bien, recargar la página antes de clasificarlo bug.
- Verificar que no se vea sospechosamente corto o vacío.
- Verificar lógica AND (vertical) / OR (horizontal).
- Si el markdown tiene bug, el árbol se considera bug por defecto.
- Nodos de exclusión: requeridos si hay criterios de "Not Medically
  Necessary" listados explícitamente. NO requeridos para
  procedimientos Experimental/Investigational/Unproven.

PASO 10 - Registrar resultados en gdoc (carpeta Validaciones,
subcarpeta del payer). Orden: primero ES, luego EN. Cada uno en dos
bloques copiables (nombre de archivo + contenido).

PASO 11 - Registrar resultados en el spreadsheet: Codes Correct,
Markdown, Flows — los tres juntos en un mensaje, cada uno en su
propio bloque copiable.

PASO 12 - Grabar video del bug (aplica desde MCG PIPA en adelante,
y retroactivo para Humana).