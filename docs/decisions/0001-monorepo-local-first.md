# ADR-0001 — Monorepo local-first, sem backend por enquanto

## Contexto
O app é hoje de uso pessoal (um único usuário). Existe intenção futura de
virar SaaS multi-usuário.

## Problema
Como estruturar o projeto para não pagar custo de backend/infra agora, sem
travar a evolução para SaaS depois?

## Alternativas
1. App single-package sem monorepo, backend adicionado depois via reescrita.
2. Monorepo (pnpm + Turborepo) com `packages/domain` compartilhável e
   persistência local (IndexedDB) hoje, `apps/api` adicionado depois.

## Decisão
Opção 2. `packages/domain` e o formato `ProgressExport` são desenhados desde
já como o contrato que uma futura API usaria, evitando reescrita de schema
quando o backend existir.

## Trade-offs
Ganho: caminho de evolução mecânico para SaaS. Custo: um pouco mais de
cerimônia de monorepo (workspaces, Turborepo) do que estritamente necessário
para o escopo atual — aceito porque a extração futura é um objetivo explícito
do usuário, não especulação.

## Como validar
Quando o backend for adicionado, `packages/domain` deve precisar de zero
mudanças estruturais — só uma nova camada de transporte (HTTP) por cima.
