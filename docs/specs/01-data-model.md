# 01 — Modelo de dados

Vive em `packages/domain`. São tipos puros TypeScript + schemas de validação
(zod) — sem dependência de UI ou de storage concreto. `apps/web` implementa o
adapter de persistência (IndexedDB); `packages/srs` consome só os tipos.

## Part (Parte do curso)

```ts
type Part = {
  id: string;            // "parte-1"
  order: number;
  title: string;         // "COMO UM SÊNIOR PENSA"
  monthHint?: "set" | "out" | "nov" | "dez";
};
```

## Lesson (Aula)

```ts
type Lesson = {
  id: string;             // "aula-06"
  partId: string;
  order: number;          // 1..42
  title: string;          // "Strategy: 'o algoritmo muda'"
  blocks: ContentBlock[]; // conteúdo completo e fiel
  theme: string;          // chave usada no Placar (Parte XV), ex: "Design Patterns"
};

type ContentBlockKind =
  | "idea" | "why" | "scenario" | "investigation" | "tool"
  | "tradeoff" | "trap" | "mission" | "experiment" | "curiosity" | "reference";

type ContentBlock = {
  id: string;
  kind: ContentBlockKind;   // mapeia os ícones 🧠🤔🌎🔍🛠️⚖️🚨🎯🧪💡📚
  markdown: string;          // texto/código fiel ao original (inclui ``` fences)
};
```

## Question (Quiz)

```ts
type Question =
  | MultipleChoiceQuestion
  | TradeoffQuestion
  | ScenarioQuestion;

type MultipleChoiceQuestion = {
  id: string;
  lessonId: string;
  type: "multiple_choice";
  prompt: string;
  options: { id: string; text: string }[];
  correctOptionId: string;
  explanation: string;       // por que está certo/errado — sempre mostrado após responder
};

type TradeoffQuestion = {
  id: string;
  lessonId: string;
  type: "tradeoff";
  givenSolution: string;     // "Adicionar cache Redis na listagem de produtos"
  options: { id: string; text: string }[]; // custos plausíveis
  correctOptionId: string;   // o custo real que essa solução introduz
  explanation: string;
};

type ScenarioQuestion = {
  id: string;
  lessonId: string;
  type: "scenario";
  scenario: string;
  architectureA: string;
  architectureB: string;
  guidingQuestions: string[];   // perguntas que o usuário deve responder na justificativa
  rubric: string[];             // checklist de autoavaliação (sem "resposta certa" única)
};
```

## QuizAttempt

```ts
type QuizAttempt = {
  id: string;
  lessonId: string;
  startedAt: string;    // ISO
  finishedAt: string;
  answers: {
    questionId: string;
    // multiple_choice / tradeoff:
    selectedOptionId?: string;
    correct?: boolean;
    // scenario:
    freeTextJustification?: string;
    selfRating?: 1 | 2 | 3 | 4 | 5; // autoavaliação usando a rubric
  }[];
  scorePercent: number;         // ignora scenario no cálculo objetivo, conta à parte
};
```

## ReviewCard (estado de SRS por aula)

```ts
type ReviewCard = {
  lessonId: string;
  easeFactor: number;      // SM-2, inicia em 2.5
  intervalDays: number;    // dias até a próxima revisão
  repetitions: number;     // acertos consecutivos
  dueDate: string;         // ISO date
  lastReviewedAt?: string;
  lastQuality?: 0 | 1 | 2 | 3 | 4 | 5; // último grade aplicado (auto ou manual)
  manualOverrideAt?: string;           // se o usuário corrigiu manualmente
};
```

## StudySession (Pomodoro)

```ts
type StudySession = {
  id: string;
  lessonId: string | null;   // null = sessão livre, não vinculada a uma aula
  kind: "focus" | "short_break" | "long_break";
  startedAt: string;
  plannedDurationSec: number;
  actualDurationSec: number; // pode ser < planejado se interrompido
  completed: boolean;
};
```

Agregação (derivada, não persistida): tempo total por `lessonId` e por `theme`
= soma de `actualDurationSec` de sessões `kind: "focus"` e `completed: true`.

## JournalEntry (Diário de Engenharia / ADR — Aula 4 e Parte XII)

```ts
type JournalEntry = {
  id: string;
  createdAt: string;
  lessonId?: string;         // opcional: pode ser um ADR livre
  title: string;
  situation: string;
  firstIdea: string;
  questionsAsked: string;
  findings: string;
  alternatives: string;
  decision: string;
  whatIdDoDifferently: string;
};
```

## ScoreboardEntry (Placar — Parte XV)

```ts
type ScoreboardEntry = {
  theme: string;              // "Design Patterns", "DDD", "Outbox"...
  month: "set" | "out" | "nov" | "dez";
  level: 0 | 1 | 2 | 3 | 4 | 5 | 6; // escala definida no material
};
```

## ProgressExport (import/export)

```ts
type ProgressExport = {
  schemaVersion: 1;
  exportedAt: string;
  reviewCards: ReviewCard[];
  quizAttempts: QuizAttempt[];
  studySessions: StudySession[];
  journalEntries: JournalEntry[];
  scoreboard: ScoreboardEntry[];
};
```

> `Lesson`/`Question` (conteúdo) **não** entram no export — são dados de
> conteúdo versionados com o app (`packages/content`), não progresso pessoal.
> Isso mantém o export pequeno e evita conflito de versão de conteúdo vs.
> versão de progresso.
