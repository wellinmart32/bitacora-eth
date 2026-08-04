# Reglas confirmadas — historial de evolución

Formato: cada regla tiene un ESTADO ACTUAL (la que se aplica hoy) y,
cuando aplica, un HISTORIAL con la versión anterior marcada
[SUPERADA], explicando por qué cambió y quién lo confirmó. Nada se
borra, solo se marca como superado.

---

## Alcance de validación por breadcrumb

**Estado actual:** Vigente.
Toda la validación se hace únicamente contra la subsección
específica que indica el breadcrumb del título del guideline, no
contra todo el documento.

**Origen:** explicación informal de Ronny, reforzada en la práctica
al consultar filas específicas con Ben (ej. fila 1.1.2, 7 de abril
2026). Si surge duda seria en un caso nuevo, confirmar con Ben.

---

## Decision tree es bug por defecto si el markdown es bug

**Estado actual:** Vigente, pero es regla de metodología INTERNA,
no confirmada explícitamente por Ben. Se aplica por lógica: el
árbol se genera del markdown, si el insumo está mal el árbol no
puede certificarse sin verificarlo aparte.

**Nota:** pendiente confirmar explícitamente con Ben si se
cuestiona.

---

## Inferencia de ICD-10 en documentos Carelon (headers clínicos)

**Estado actual:** Vigente (confirmado por Ben Beidler).
La frase "Refer to the ICD-10 CM manual" en Carelon es IRRELEVANTE.
La extracción real ocurre por inferencia clínica a nivel de header
dentro de Clinical Indications. Comportamiento ESPERADO, no bug por
defecto. Solo es bug si la inferencia es clínicamente NO relacionada
con el header.

Cita de Ben: "the 'refer to the icd10 cm manual' portion here is
irrelevant since we are extracting ICD10s from the headers."
Cita de Ben sobre cuándo sí es bug: "IF totally unrelated we should
flag them. That would indicate that the inference is broken."

**[SUPERADA]** Antes se marcaba como bug automático toda inferencia
de ICD-10 en Carelon, sin evaluar si era clínicamente apropiada.
Pendiente: re-evaluar fila 94.1 bajo el criterio actualizado.

Sobre fuentes nuevas: Ben no tiene lista maestra. Su indicación:
"I do not have that list. I would say ask Kevin as you pick up new
guideline sources." → escalar a Kevin, no a Ben.

---

## Regla de versión del documento

**Estado actual:** Vigente (confirmada por Uzo, 16 de junio de
2026).
Los códigos se validan contra la versión que el sistema usó al
extraer, NO la más reciente. Si una versión nueva agrega códigos,
NO es bug: se anota como NOTA. Mantener códigos al día es trabajo
del pipeline de ingeniería.
Cómo saber la versión: fijarse en la URL fuente / fecha efectiva
del guideline, no la más reciente publicada por el payer.
Aplicada retroactivamente en fila 120.1 (CMS): ICD-10 reclasificado
de bug a correcto + nota.

---

## Nodos de exclusión para procedimientos experimentales

**Estado actual:** Vigente (confirmada por Uzo, 18 de mayo de
2026).
Experimental/Investigational/Unproven NO requieren nodos de
exclusión, porque el flujo no matchea ningún guideline si se
solicitan. Aplica SOLO a procedimientos experimentales; otros
criterios de "Not Medically Necessary" listados explícitamente
siguen requiriendo nodos de exclusión.
---

## Subsecciones "Definitions and General Guidelines" (CMM)

**Estado actual:** Vigente (confirmada por Uzo, 26 de junio de
2026).
En documentos CMM de cirugía (CMM-311, CMM-312, CMM-313), la
subsección "Definitions and General Guidelines" NO hereda la tabla
de Codes a nivel de documento. Los códigos pertenecen a las
subsecciones de procedimiento específico, no al guideline de
definiciones. Si el sistema le asigna la tabla completa al
guideline de definiciones, es bug de alcance.
Uzo lo planteó también como posible enhancement request para
dividir mejor estos documentos.

---

## Regla de tablas con bloque general

**Estado actual:** Vigente.
Cuando una tabla de códigos no separa por subsección de Medical
Necessity, las subcategorías iniciales sin título de categoría
arriba forman un bloque general que aplica transversalmente a las
subsecciones que NO tengan su propia subtabla nombrada.

Ejemplos: CPB 0516 de Aetna (Colorectal Cancer Screening);
CG-SURG-01 de Anthem (Colonoscopy).

Cómo aplicar: identificar la subsección del breadcrumb → buscar si
tiene subtabla propia en la tabla de códigos → si SÍ, evaluar contra
esa subtabla; si NO, evaluar contra el bloque general.

---

## Inconsistencia entre filas hermanas (bloque general)

**Estado actual:** Vigente.
Cuando varias subsecciones comparten un bloque general de códigos,
el sistema debe discriminar consistentemente por lógica clínica en
todas ellas. Si discrimina en una subsección pero no en otra del
mismo documento, es bug por inconsistencia.
Ejemplo: Anthem CG-SURG-01, filas 93.1 vs 93.2 vs 93.3.

---

## Condicional "Not Covered" que describe el alcance entero

**Estado actual:** Vigente (confirmada por Uzo).
Si un condicional tipo "Not Covered if used to report X" describe
el tema general/alcance entero del guideline (no un caso puntual),
equivale a Not Covered absoluto → NO extraer.
Ejemplo: Cigna 0051 (Bariatric Surgery).

Regla práctica: si la condición describe el tema general del
guideline, equivale a absoluto. Si describe un caso específico, es
condicional verdadera.

---

## Rangos ICD-10 con dígitos

**Estado actual:** Vigente (confirmada por Uzo).
Un código extraído debe tener el mismo número de dígitos que el
rango original. Ejemplo: rango Z86.0100-Z86.0109 NO permite el
código Z86.010 (menos dígitos).

---

## Duplicados del mismo guideline

**Estado actual:** Vigente.
Mismo source document + mismo breadcrumb + mismo contenido, solo
difieren en Guideline ID y Version → notificar a Ben, quien
usualmente descopa el duplicado. Validar solo la versión más
reciente.