# Qué contiene cada documento

Última actualización: 04 de agosto de 2026

## Propósito

Este repositorio es el respaldo permanente y vivo de todo el
trabajo laboral del proyecto Ethermed QA. Reemplaza la necesidad de
mantener chats antiguos como referencia: cuando un chat de trabajo
activo (ticket, validación, reunión) termina, lo importante se
traslada aquí y el chat se puede eliminar sin perder información.

## Carpetas de este repositorio

### indice-general/
La puerta de entrada. Contiene:
- `que-contiene-cada-documento.md` (este archivo)
- `indice-numeracion-chats.md` — numeración de chats por categoría
- `changelog-general.md` — cambios grandes a la estructura

### validacion-excel-payers/
Todo lo relacionado a la actividad de validar guidelines extraídos
por el sistema contra los documentos originales de cada payer
(Aetna, Cigna, Humana, Anthem Georgia, BCBS, CMS, MCG). Contiene:
- `proceso-de-validacion.md` — los 12 pasos del proceso vigente
- `bugs-sistematicos.md` — patrones de bugs conocidos
- `terminologia-y-payers.md` — glosario y estructura por payer
- `reglas-confirmadas-historial.md` — reglas con historial de
  evolución (qué cambió, quién lo confirmó, cuándo)

### tickets-qa/
Todo lo relacionado a la actividad de testing de tickets Jira sobre
funcionalidades del sistema Ethermed (no filas del Excel). Contiene:
- `registro-de-tickets.md` — ficha de cada ticket trabajado
- `reglas-y-aprendizajes-de-testing.md` — aprendizajes generales de
  este tipo de testing, reutilizables entre tickets

### automatizacion/
Todo lo relacionado al trabajo de automatización de pruebas junto a
Oscar (equipo Hopper), surgido de un pivote pedido por Dom
Garbellano. Contiene:
- `ambiente-local.md` — configuración del ambiente local (WSL,
  herramientas, repositorio, variables de entorno)
- `suite-regression-api.md` — historial y hallazgos técnicos de la
  suite de regression testing de API
- `pendientes.md` — tareas bloqueadas o por definir dentro de este
  trabajo

### herramienta-verificacion/
Todo lo relacionado al proyecto personal `guideline_verification_tool`
(repo separado: alex95mf/guideline_verification_tool), la herramienta
de automatización en Python para agilizar la validación de
guidelines. Contiene:
- `estado-del-proyecto.md` — arquitectura, estado de los 6 módulos,
  tickets Jira asociados, y hallazgos técnicos

### (Futuras carpetas)
Cuando aparezca una actividad laboral nueva y distinta a las de
arriba, se crea una carpeta nueva en este mismo repositorio y se
agrega su entrada aquí.