# Índice de numeración de chats

Regla: los números nunca se reciclan ni se reinician, aunque se
borre un chat. El siguiente número de una categoría siempre es el
último usado + 1, sin importar cuántos chats de esa categoría ya
se hayan eliminado.

Solo llevan número las categorías que se repiten SIN un
identificador propio (un ticket ya es único por su número, una
reunión ya es única por su fecha — esas NO necesitan contador).

## Categorías con contador

- Validación Excel [Payer] → último número usado: 0
- Automatización (guideline_verification_tool) → último número
  usado: 0
- (agregar categorías nuevas aquí conforme aparezcan)

## Categorías sin contador (usan su propio identificador)

- Ticket [número de Jira] → ej. "Ticket TT-303"
- Reunión [rango de fechas] → ej. "Reunión 20abr-26abr"