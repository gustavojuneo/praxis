# 11 — Roteiro para virar SaaS (futuro)

Não implementar agora. Este documento existe para que decisões de hoje não
travem o amanhã.

## O que já está pronto para isso

- `packages/domain`: tipos e schemas zod compartilháveis entre frontend e
  um futuro backend (mesmo pacote pode ser publicado/importado por
  `apps/api`).
- `ProgressExport` (spec 01/08): já é, na prática, o formato de payload de
  sincronização. Um endpoint `POST /sync` recebendo/devolvendo esse mesmo
  shape substitui o import/export manual sem mudar o schema.
- TanStack Query já usado para "server state" mesmo hoje sendo local
  (IndexedDB) — trocar a `queryFn`/`mutationFn` de "ler IndexedDB" para
  "chamar fetch(/api/...)" é uma troca de adapter, não uma reescrita de
  telas.

## Passos previstos, quando fizer sentido

1. `apps/api` (Node — Hono ou Fastify) no mesmo monorepo, consumindo
   `packages/domain` para validação de payload.
2. Autenticação simples (magic link ou OAuth) — usuário único vira multi-tenant.
3. Persistência real (Postgres) espelhando as entidades de `packages/domain`.
4. Sincronização multi-dispositivo: o `ProgressExport` de hoje vira o
   contrato de `GET/POST /progress`, com merge no servidor usando a mesma
   lógica descrita na spec 08.
5. Conteúdo (`packages/content`) passa a ser servido por API também, abrindo
   caminho para múltiplas trilhas/cursos além do plano de engenharia sênior
   (se isso vier a ser um objetivo de produto).

## O que evitar até lá

Não adicionar autenticação, multi-usuário ou chamadas de rede "por
precaução". Seguindo a Aula 1 do próprio material: só resolva o problema que
existe hoje (uso pessoal, local-first) com a menor complexidade necessária.
