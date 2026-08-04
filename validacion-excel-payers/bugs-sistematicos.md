# Bugs sistemáticos identificados

Esta lista se actualiza conforme se encuentran nuevos patrones.
Conocerlos ayuda a detectarlos rápido en filas nuevas.

## 1. ICD-10 header/parent codes no billables
Payers afectados: Cigna, Anthem Georgia
Patrón: el sistema incluye códigos parent de 3 caracteres (ej. Z01)
cuando solo los códigos con extensiones (Z01.810, Z01.811) son
billables. Solo estos últimos deberían extraerse.
Filas ejemplo: Cigna 53.1, 56.1; Anthem 92.1

## 2. Humana Medicare Advantage: jurisdicción J15 confundida con
ICD-10 J15
Payer afectado: Humana Medicare Advantage
Patrón: el extractor confunde el código de jurisdicción Medicare
"J15" (CGS Administrators) con el código ICD-10 J15 (neumonía).
Filas afectadas: 11.1, 13.1, 14.1, 15.1, 47.1, 48.1
Estado: Resuelto. Confirmado arreglado en las 6 filas (ticket
TT-305, cerrado).

## 3. Cigna scope violation
Payer afectado: Cigna
Patrón: el sistema sobre-incluye contenido de subsecciones hermanas
en la subsección específica del breadcrumb.
Filas ejemplo: Cigna 56.1, 59.1

## 4. Humana Commercial: omisiones sistemáticas
Payer afectado: Humana Commercial
Patrón: omisión consistente de HCPCS y de exclusiones de Coverage
Limitations en los árboles de decisión.

## 5. Inconsistencia al discriminar subsecciones con bloque general
Payers afectados: Anthem Georgia
Patrón: el sistema demuestra capacidad de discriminar por lógica
clínica en algunas subsecciones pero no en otras del mismo
documento, generando sobre-inclusión.
Ejemplo: Anthem 93.1 vs 93.2 vs 93.3 (Colonoscopy).

## 6. ICD-10 inferenciales en Carelon — clínicamente no relacionados
Payer afectado: Anthem Georgia (documentos Carelon)
Patrón: la inferencia clínica de ICD-10 a nivel de header es
comportamiento ESPERADO, no bug por defecto. Este bug aplica
únicamente cuando el sistema infiere códigos ICD-10 clínicamente
NO relacionados con el header (ej. familia completa de códigos de
conjuntivitis en contexto oncológico).
Estado: fila 94.1 (Upper GI Endoscopy) pendiente de re-evaluación
individual código por código bajo el criterio actualizado.

## 7. Anthem Georgia / Carelon (Torticollis, fila 98.11): omisión
de inferencia ICD-10
Patrón: en un caso donde otros documentos Carelon similares sí
disparan inferencia clínica de ICD-10 a nivel de header, esta fila
devolvió extracción completamente vacía. Bug por inconsistencia
frente a filas comparables (ej. 94.1).

## Nota general
Cuando se detecta un patrón nuevo que se repite en más de una fila
o más de un payer, agregarlo aquí con: payer(s) afectado(s), patrón
descrito en una o dos líneas, filas ejemplo, y estado (activo /
resuelto / pendiente de confirmación).