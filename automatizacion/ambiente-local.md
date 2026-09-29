# Ambiente local de automatización

Ambiente configurado en WSL/Ubuntu dentro de Windows para el
trabajo de automatización con Oscar (equipo Hopper).

## Herramientas (asdf)

- Elixir 1.19.4-otp-28
- Erlang 28.2
- Node 20.20.0
- Python 3.12.6
- Bun 1.3.4

## Otros componentes

- Oban Pro configurado con hex.repo
- PostgreSQL 18 + pgvector instalado localmente
- Docker Desktop con integración WSL activa

## Repositorio

- Ruta local: ~/ethermed
- Usuario GitHub: amartinez-eth (fine-grained token)
- Acceso resuelto vía ticket ITOPS-94, cerrado por Stacey Fontenot
  el 9 sep 2026

## Variables de entorno permanentes (~/.bashrc)

- DATABASE_URL
- CLOAK_KEY
- ETHERMED_SMOKE_API_BASE_URL
- ETHERMED_SMOKE_API_TOKEN