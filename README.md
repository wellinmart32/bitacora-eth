# bitacora-eth

Respaldo permanente del trabajo de QA en el proyecto Ethermed.

## Para qué sirve este repositorio

Este repositorio guarda todo lo importante que se va aprendiendo y
confirmando durante el trabajo diario en Ethermed: procesos, reglas
del equipo, bugs conocidos, y el historial de cómo evolucionó cada
criterio con el tiempo.

Existe para poder trabajar en chats nuevos sin perder contexto, y
para poder eliminar chats viejos sin miedo a perder información
importante. Cuando un chat de trabajo activo (un ticket, una
validación, una reunión) termina, lo relevante se traslada aquí
antes de borrar ese chat.

## Estructura

- **indice-general/** — punto de entrada: qué contiene cada
  carpeta, numeración de chats, y registro de cambios grandes a
  este repositorio.
- **validacion-excel-payers/** — todo sobre la validación de
  guidelines extraídos por el sistema contra los documentos
  originales de cada payer (Aetna, Cigna, Humana, Anthem Georgia,
  BCBS, CMS, MCG).
- **tickets-qa/** — todo sobre el testing de tickets Jira de
  funcionalidades del sistema Ethermed (no relacionado a filas del
  Excel).

Cuando aparece un tipo de trabajo nuevo y distinto a los dos
anteriores, se agrega una carpeta nueva siguiendo el mismo patrón.

## Cómo se actualiza

Nada se borra cuando algo cambia. Se marca la versión anterior como
superada y se explica qué cambió, quién lo confirmó, y cuándo — así
queda el historial completo de cómo evolucionó cada regla o
proceso.