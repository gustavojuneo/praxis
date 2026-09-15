# AGENTS.md — apps/web

Convenções específicas do app. Leia o `AGENTS.md` da raiz primeiro.

## Organização (Vertical Slice — Aula 19 aplicada)

```
src/
├── routes/                    # TanStack Router (file-based)
│   ├── __root.tsx
│   ├── index.tsx               # Home / Sessão de hoje
│   ├── trilha/
│   │   ├── index.tsx           # lista Partes → Aulas
│   │   └── $lessonId.tsx       # conteúdo completo da aula
│   ├── revisao/index.tsx       # fila de revisão SRS (due today)
│   ├── quiz/$lessonId.tsx      # runner de quiz
│   ├── diario/                 # journal / ADR do usuário
│   ├── placar/index.tsx        # scoreboard Parte XV
│   └── configuracoes/index.tsx
├── features/
│   ├── pomodoro/
│   │   ├── api/                # persistência de StudySession
│   │   ├── components/         # PomodoroRing, PomodoroControls, SoundAlert
│   │   ├── hooks/               # usePomodoroTimer (máquina de estados)
│   │   └── model.ts             # tipos locais da feature
│   ├── quiz/
│   │   ├── components/          # MultipleChoiceCard, TradeoffCard, ScenarioCard
│   │   ├── hooks/                # useQuizRunner
│   │   └── grading.ts            # regras de correção por tipo
│   ├── srs/
│   │   ├── api/                  # lê/escreve ReviewCard via packages/srs
│   │   └── components/           # DueBadge, ReviewQueueList
│   ├── content/
│   │   ├── components/           # LessonBlock (renderiza por ícone), CodeBlock
│   │   └── api/                   # lê packages/content
│   ├── journal/
│   └── progress/                  # export/import, scoreboard, stats
├── components/                    # composições de apps/web que usam packages/ui
├── lib/
│   ├── db.ts                       # wrapper IndexedDB (idb)
│   └── queryClient.ts
└── styles/
    └── tokens.css                   # tokens semânticos (ver spec 10)
```

## Estado — classifique antes de escrever código (Aula 31)

| Tipo de estado | Onde vive |
|---|---|
| UI (modal aberto, tab ativa) | `useState` local |
| Formulário (respostas do quiz em andamento) | `useState`/`useReducer` local ao runner |
| URL (filtro da trilha, aula aberta) | TanStack Router search params |
| Servidor (aulas, ReviewCards, StudySessions — mesmo vindo do IndexedDB local) | TanStack Query |
| Derivado (tempo total por tema, próximo item due) | calculado via `select` do Query ou hook memoizado — nunca duplicado em state próprio |

## Pomodoro — cuidados de plataforma

- Timer não pode depender só de `setInterval` (drift + tabs em background):
  calcule o tempo restante a partir de `endTimestamp - Date.now()`, não por
  contagem de ticks.
- Som ao final do bloco: `<audio>` com fallback silencioso se o navegador
  bloquear autoplay (pedir uma interação inicial do usuário para "destravar" áudio).
- Ao trocar de aba/app (Page Visibility API), continue contando — não pause
  silenciosamente sem avisar.

## Testes mínimos por feature nova

- `packages/srs`: testes de unidade cobrindo os casos de borda do algoritmo
  (primeira revisão, streak de acertos, um erro depois de vários acertos,
  override manual "sei de cor" e "ainda inseguro").
- `features/quiz/grading.ts`: um teste por tipo de questão.
- `features/pomodoro`: teste do hook de timer com fake timers.
