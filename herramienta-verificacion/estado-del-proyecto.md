# guideline_verification_tool — Estado del proyecto

Herramienta personal de automatización en Python para agilizar la
validación de guidelines extraídos por Ethermed. Repo separado en
GitHub: alex95mf/guideline_verification_tool (no confundir con este
repositorio de respaldo bitacora-eth).

## Arquitectura

Sistema de dos capas:
- **Motor determinístico** (Módulo 2): reglas duras para bugs
  sistemáticos ya conocidos (ej. Z01 parent codes, confusión J15
  jurisdicción/ICD-10). Tiene autoridad de override sobre la capa
  de votantes.
- **Capa de votantes LLM** (Fase 2): 3 agentes independientes para
  juicio contextual — directo, inverso, y segundo modelo. Producen
  niveles de confianza: verde (todos de acuerdo), amarillo (minoría
  difiere), rojo (mayoría difiere). Sirve como asistente de triage,
  NO como veredicto final.

## Estado de módulos

1. **Parser** (`parser.py`) — completo. Limpia el ruido de UI del
   backoffice usando un ancla del sidebar ("Backoffice"), extrae
   metadata por coincidencia de labels fijos, separa secciones de
   código por detección de headers. Transcribe bugs tal cual (no
   los corrige) — comportamiento esperado del módulo.
2. **Motor de reglas** (Z01, J15) — completo.
3. **Extracción de markdown/árbol de decisión** — completo.
4. **Librería de fuentes + evaluación con IA** — completo.
5. **Generador de reportes ES/EN** — completo.
6. **Automatización de navegador (Playwright)** — en progreso.
   Las 3 funciones (`search_source_documents`,
   `get_processing_status`, `start_processing`) están implementadas
   en `src/browser_automation.py`. Falta el menú interactivo de
   consola en `src/main.py` que las integre.

## Tickets Jira por módulo

- TT-434 — Punto 3 (verificación de existencia de guideline por
  CPT code)
- TT-435 — Módulo 1 (Parser)
- TT-436 — Módulo 2 (Motor de reglas Z01/J15)
- TT-437 — Módulo 3 (Extracción markdown/árbol)
- TT-439 — Módulo 4 (Librería de fuentes + evaluación IA)
- TT-440 — Módulo 5 (Generador de reportes ES/EN)
- TT-441 — Módulo 6 (Automatización de navegador)

## Hallazgos técnicos

- El backoffice de Ethermed usa Phoenix LiveView: los enlaces de
  navegación no son `<a href>` estándar, sino atributos `phx-click`
  con URLs codificadas en JSON. Hubo que parsearlos con regex y
  deduplicar resultados por URL para que
  `search_source_documents` funcionara.
- La API key de Anthropic se maneja vía variable de entorno
  `ANTHROPIC_API_KEY`, con fallback a modo mock si no está
  configurada.

## Próximo paso

Construir el menú interactivo de consola en `src/main.py`,
integrando las 3 funciones del Módulo 6 (opciones: correr
comparación, buscar/procesar documentos sin procesar, salir).