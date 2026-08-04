# Terminología y estructura por payer

## Términos clave

- **Payer**: aseguradora médica (Aetna, Cigna, Humana, Anthem, MCG,
  BCBS, CMS)
- **Guideline Document**: documento crudo original obtenido de
  fuentes externas. Materia prima antes de ser procesada.
- **Guideline**: documento ya procesado y extraído por el sistema.
  Contiene códigos, markdown y decision tree. Cada fila del
  spreadsheet corresponde a un guideline.
- **Breadcrumb del título**: estructura de niveles separados por >
  en el campo Title del guideline, indica la subsección específica
  del documento original. Toda la validación se hace únicamente
  contra esa subsección.
- **Markdown**: texto extraído de la sección Policy del documento
  original. Representación fiel y completa de los criterios
  médicos relevantes de la subsección indicada por el breadcrumb.
- **Decision Tree**: árbol de decisiones generado del markdown.
  Vertical = AND. Horizontal = OR.
- **Header (Carelon)**: subtítulo de condición clínica dentro de
  "Clinical Indications" en documentos Carelon. Cada header es un
  guideline separado en el backoffice.
- **Bloque general de tabla**: cuando una tabla de códigos no
  separa por subsección, las subcategorías iniciales sin título de
  categoría aplican transversalmente a las subsecciones sin
  subtabla propia.

## Payers activos del proyecto

- Aetna (documentos CPB)
- Cigna / Evicore (proveedor externo, estructura similar a Carelon)
- Humana Commercial
- Humana Medicare Advantage (bug sistemático J15, ver
  bugs-sistematicos.md)
- Anthem Georgia (directos CG-XX + Carelon Medical Benefits Mgmt)
- Anthem New York
- BCBS Illinois (HCSC)
- BCBS Texas
- CMS (NCD/LCD)
- MCG PIPA (delegated entity)

## Estructura por payer

### Aetna
Documentos: CPB-XXXX. Subsecciones de Medical Necessity con tablas
de códigos separadas o con tabla de bloque general.

### Humana
Commercial y Medicare Advantage. En Medicare Advantage usa códigos
de jurisdicción tipo "J15" que NO son ICD-10.

### Cigna
Documentos con identificadores numéricos (ej. 0051). Secciones de
Medical Necessity y Coverage Limitations. Frecuente "Not Medically
Necessary" condicional.

### Anthem Georgia
Dos tipos de documentos:
- **Anthem CG-XX** (directos): tablas CPT/HCPCS/ICD-10 específicas
  o "All diagnoses".
- **Carelon Medical Benefits Management** (externo): UNA tabla
  general de CPT/HCPCS al final que aplica a todas las
  subsecciones. ICD-10 dice "Refer to the ICD-10 CM manual" pero es
  irrelevante — la extracción real ocurre por inferencia clínica a
  nivel de header.

### MCG (PIPA)
Documentos delegated entity, estructura específica del proveedor.

## Excel row ID (clarificación)

Cuando se pide el "id" o "número único" de una fila, se refiere al
número de fila del Excel/Testing Log (ej. 138 para fila 2.2.1), NO
al Guideline UUID del backoffice.

BCBS Illinois mapping: 138=2.2.1, 139=2.2.2, 140=44.1, 141=44.2,
142=55.1.1, 143–146=55.1.2–55.1.5, 147=57.1, 148=57.2.