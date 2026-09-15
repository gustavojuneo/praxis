# Senior Track

App de estudo diário para o plano "Engenharia Sênior na Prática" — Pomodoro +
quiz + repetição espaçada, com as 42 aulas na íntegra.

**Comece por `AGENTS.md`** — é o guia de orientação para qualquer agente
(Claude Code, Claude Design) ou pessoa trabalhando neste repositório.

## Leitura recomendada, em ordem

1. `AGENTS.md` — convenções gerais do monorepo
2. `docs/specs/00-overview.md` — visão de produto
3. `docs/specs/01-data-model.md` a `08-import-export.md` — specs funcionais
4. `docs/specs/09-navigation-ia.md` — mapa de telas
5. `docs/specs/10-design-brief-claude-design.md` — briefing para gerar a UI
6. `docs/specs/11-roadmap-saas.md` — o que fica para depois

## Estrutura

```
apps/web            → o app React
packages/domain      → tipos e schemas compartilhados
packages/content      → conteúdo das 42 aulas (JSON, gerado da fonte)
packages/srs           → algoritmo de repetição espaçada
packages/ui              → componentes shadcn compartilhados + tokens
docs/source                → plano didático original (fonte de verdade)
docs/specs                  → specs funcionais
docs/decisions                → ADRs do próprio app
skills/                         → skills deste projeto (extração de conteúdo,
                                   autoria de quiz, auditoria de SRS)
```

## Comandos

```bash
pnpm install
pnpm dev
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```
