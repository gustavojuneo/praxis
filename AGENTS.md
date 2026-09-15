# AGENTS.md — Senior Track

> Este arquivo orienta qualquer agente (Claude Code, Claude Design, ou humano) que
> trabalhe neste monorepo. Leia isto antes de tocar em código.

## O que é este projeto

Um app de estudo para o plano didático "Engenharia Sênior na Prática"
(42 aulas, setembro–dezembro). Não é um leitor de markdown: é uma ferramenta de
prática diária com **Pomodoro**, **quiz avaliativo** e **repetição espaçada (SRS)**,
para transformar as 42 aulas em hábito e retenção real.

Fonte de verdade do conteúdo pedagógico: `docs/source/plano-didatico.md`
(cópia fiel do material original — nunca editar o conteúdo pedagógico "à mão"
sem passar pela skill `lesson-content-extractor`).

## Princípios não-negociáveis

1. **Fidelidade de conteúdo.** As 42 aulas devem estar completas no app — sem
   resumir, sem cortar blocos (🧠🤔🌎🔍🛠️⚖️🚨🎯🧪💡📚). "App grande, mas fiel" é uma
   decisão de produto, não um detalhe técnico.
2. **Dogfooding do próprio método.** O app aplica os conceitos que ensina:
   - Vertical Slice na organização de features (Aula 19)
   - Tokens semânticos no design system (Aula 36)
   - Estado classificado por tipo — UI/Form/URL/Server/Derived (Aula 31)
   - ADR para decisões arquiteturais do próprio app (Aula 4), guardadas em
     `docs/decisions/`.
   Se uma decisão de arquitetura do app contradiz um princípio do próprio
   material, isso é um cheiro de código — pare e questione.
3. **Local-first hoje, backend-ready amanhã.** Não existe backend ainda. Toda
   persistência é local (IndexedDB), mas os contratos de dados
   (`packages/domain`) são desenhados para virarem payloads de API sem
   reescrita, quando o SaaS existir (ver `docs/specs/11-roadmap-saas.md`).
4. **Sem cerimônia arquitetural.** Nada de `IRepository` + `Repository` para
   tudo (Aula 17). Só crie abstração quando houver variação real hoje.

## Stack

- **Monorepo:** pnpm workspaces + Turborepo
- **Linguagem:** TypeScript estrito (`tsconfig.base.json`)
- **UI:** React + Vite, Tailwind CSS, `tailwind-merge`, `tailwind-variants`, shadcn/ui
- **Roteamento:** TanStack Router (file-based, com `routeTree.gen.ts` gerado)
- **Dados/estado servidor:** TanStack Query (mesmo sem backend real — os
  "query functions" leem/escrevem no adapter local; troca para HTTP depois é
  mecânica, não estrutural)
- **Persistência local:** IndexedDB via `packages/domain` (adapter pattern —
  ver spec 01 e 11)
- **Lint/format:** Biome (sem ESLint/Prettier)
- **Testes:** Vitest + Testing Library
- **Gerenciador:** pnpm

## Estrutura do monorepo

```
senior-track/
├── AGENTS.md                  ← você está aqui
├── apps/
│   └── web/                   ← o app (responsivo, mobile + desktop)
│       └── AGENTS.md          ← convenções específicas do app
├── packages/
│   ├── domain/                ← tipos e contratos compartilhados (entidades)
│   ├── content/                ← conteúdo das 42 aulas (JSON gerado da fonte)
│   ├── srs/                    ← algoritmo de repetição espaçada (puro, sem UI)
│   ├── ui/                     ← componentes shadcn compartilhados + tokens
│   └── config/                 ← tsconfig/biome compartilhados
├── docs/
│   ├── source/                 ← plano didático original (fonte de verdade)
│   ├── specs/                  ← specs funcionais (leia antes de implementar)
│   └── decisions/               ← ADRs do próprio app
└── skills/                      ← skills deste projeto (ver skills/*/SKILL.md)
```

## Ordem de leitura recomendada para um agente novo

1. `docs/specs/00-overview.md` — visão e escopo
2. `docs/specs/01-data-model.md` — entidades
3. `docs/specs/09-navigation-ia.md` — mapa de telas
4. A spec específica da feature que você for tocar (04 Pomodoro, 05 Quiz, 06 SRS...)
5. `docs/specs/10-design-brief-claude-design.md` — só se for trabalhar em UI/visual

## Comandos

```bash
pnpm install
pnpm dev          # roda apps/web em modo dev
pnpm lint         # biome check
pnpm lint:fix
pnpm typecheck
pnpm test
pnpm build
```

## Convenções de código

- Vertical slice por feature em `apps/web/src/features/<feature>/{api,components,hooks,routes}`.
- Nunca colocar lógica de negócio (SRS, cálculo de score, agregação de tempo)
  dentro de componentes React — vive em `packages/srs` ou em hooks puros
  testáveis.
- Toda função exportada de `packages/domain` e `packages/srs` precisa de teste
  unitário (é a parte crítica do produto: se o algoritmo de repetição errar,
  o app inteiro perde sentido).
- Componentes shadcn ficam em `packages/ui` e são consumidos por `apps/web`;
  não duplicar componentes-base dentro do app (ver Aula 33 — cuidado com
  `shared/` virar buraco negro, mas componentes de design system são exceção
  legítima de compartilhamento).
- Cores/spacing sempre via tokens semânticos do Tailwind (ver spec 10), nunca
  valores primitivos soltos no JSX.
- Commits: `feat(pomodoro): ...`, `fix(srs): ...`, `docs(spec): ...` (Conventional Commits).

## Definition of Done por feature

- [ ] Spec correspondente em `docs/specs/` foi lida e seguida (ou a spec foi
      atualizada, se a implementação revelou algo novo — mantenha as specs vivas)
- [ ] Responsivo (mobile e desktop testados — são prioridade igual)
- [ ] Estados de vazio/erro/carregando tratados
- [ ] Acessível: navegação por teclado e labels para leitor de tela (Aula 38)
- [ ] Testado (unitário para lógica, ao menos smoke test para telas)
- [ ] `pnpm lint` e `pnpm typecheck` limpos

## O que NÃO fazer

- Não resumir ou reescrever o conteúdo pedagógico das aulas.
- Não introduzir Redux/Zustand/etc. antes de esgotar TanStack Query + estado
  local de componente (Aula 1: não escolha ferramenta antes de investigar).
- Não criar backend ainda — isso é roadmap explícito (spec 11), não uma
  decisão implícita no meio de uma feature de UI.
