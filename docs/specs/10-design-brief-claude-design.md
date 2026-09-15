# 10 — Briefing de design (para Claude Design)

> Nenhum arquivo de design system foi anexado a este projeto. Este documento
> define tokens e diretrizes próprias, aplicando o próprio conceito de
> "tokens semânticos" do material (Aula 36) — use-o como ponto de partida e
> proponha refinamentos visuais dentro destas restrições.

## Produto em uma frase

Um app de estudo diário, limpo e sem fricção, para praticar engenharia
sênior com Pomodoro + quiz + repetição espaçada — a sensação deve ser de
**caderno de laboratório organizado**, não de curso corporativo nem de app
gamificado.

## Plataformas e prioridade

Web responsivo. **Mobile e desktop têm prioridade igual** — desenhe os dois
layouts lado a lado para cada tela chave, não "desktop primeiro e depois
encolhe".

## Tom e personalidade visual

- Limpo, calmo, baixa carga cognitiva — o usuário já está "pensando"; o
  visual não deve competir por atenção.
- Tipografia com boa leitura de texto longo (as aulas têm blocos de texto e
  código extensos) e boa diferenciação entre corpo de texto e blocos de
  código.
- Uso de cor com propósito: cada `ContentBlockKind` (idea/why/scenario/
  investigation/tool/tradeoff/trap/mission/experiment/curiosity/reference)
  deve ter uma identidade visual sutil e consistente (cor de acento + ícone),
  para o usuário reconhecer o tipo de bloco de relance — sem virar
  "arco-íris".
- Suporte a modo claro e escuro desde o início (sessões de estudo acontecem
  de dia e de noite).

## Tokens (ponto de partida — Tailwind CSS)

Tokens **semânticos**, nunca hardcode de cor primitiva nos componentes
(dogfooding da Aula 36):

```
--color-bg-canvas
--color-bg-surface
--color-bg-surface-raised
--color-border-subtle
--color-text-primary
--color-text-secondary
--color-text-inverse
--color-action-primary
--color-action-primary-hover
--color-focus-ring

--color-block-idea         (🧠)
--color-block-why          (🤔)
--color-block-scenario     (🌎)
--color-block-investigation(🔍)
--color-block-tool         (🛠️)
--color-block-tradeoff     (⚖️)
--color-block-trap         (🚨)
--color-block-mission      (🎯)
--color-block-experiment   (🧪)
--color-block-curiosity    (💡)
--color-block-reference    (📚)

--color-feedback-success
--color-feedback-error
--color-feedback-warning

--space-1 .. --space-8     (escala 4/8px)
--radius-sm / --radius-md / --radius-lg
--shadow-sm / --shadow-md
--font-sans   (UI e corpo)
--font-mono   (blocos de código)
```

Redefinir os mesmos tokens sob `[data-theme="dark"]` (nunca cores fixas
espalhadas pelo código).

## Biblioteca de componentes

Base: **shadcn/ui** (Radix + Tailwind). Componentes custom específicos do
produto a desenhar:

- **LessonBlockCard** — cartão de bloco de conteúdo, com ícone + cor do
  `ContentBlockKind`, suporta markdown com blocos de código destacados
  (fonte monoespaçada, syntax-highlight leve).
- **PomodoroRing** — anel de progresso circular com tempo restante no
  centro, estado (foco/pausa) visível por cor, controles Pausar/Pular.
- **DueBadge** — indicador de atraso de revisão (0 / 1–3 / 4+ dias), 3
  níveis visuais discretos, nunca alarmante/vermelho-berrante.
- **QuizOptionCard** — opção de múltipla escolha/trade-off, com 3 estados:
  neutro, selecionado, revelado (correto verde / incorreto vermelho +
  explicação expansível abaixo).
- **ScenarioCompareCard** — duas colunas (Arquitetura A / B) lado a lado no
  desktop, empilhadas no mobile, com textarea de justificativa abaixo e
  checklist de rubrica revelável.
- **ScoreboardGrid** — grid Tema × Mês, célula = nível 0–6 representado por
  um indicador visual gradual (não numérico cru — pense em algo como
  intensidade de preenchimento).
- **NavShell** — bottom tabs no mobile / sidebar fixa no desktop, mesmos 5
  itens (Hoje, Trilha, Revisão, Diário, Mais).

## Telas a desenhar (mobile + desktop cada)

1. **Home — Sessão de hoje** (spec 03): cartão de revisões pendentes, cartão
   de próxima aula, CTA primário "Começar Pomodoro", resumo do dia.
2. **Trilha** — lista de Partes (I a XV) colapsáveis, aulas com indicador de
   status (não iniciada / em progresso / revisada) e badge de revisão devida.
3. **Conteúdo da aula** — sequência de `LessonBlockCard`, `PomodoroRing`
   fixo/sticky enquanto rolando, CTA para iniciar quiz ao final.
4. **Quiz runner** — uma questão por vez, `QuizOptionCard` para MC/trade-off,
   `ScenarioCompareCard` para cenário, tela de resultado ao final.
5. **Fila de revisão** — lista ordenada por atraso, com `DueBadge` e ação
   rápida "Revisar" / "Ajustar revisão" (Ainda inseguro / Sei de cor).
6. **Diário de Engenharia** — lista cronológica de entradas + formulário
   template (Situação/Primeira ideia/Perguntas/Descobertas/Alternativas/
   Decisão/O que faria diferente).
7. **Placar** — `ScoreboardGrid` completo.
8. **Configurações** — durações do Pomodoro, som on/off, tema claro/escuro,
   export/import de progresso.

## Estados a cobrir em cada tela

Vazio (primeiro uso), carregando, erro (ex.: import inválido), e "tudo em
dia" (fila de revisão vazia — reforço positivo discreto, não confete).

## Acessibilidade (obrigatório, não opcional — Aula 38)

Contraste AA mínimo, navegação por teclado em todo componente interativo
(especialmente `QuizOptionCard` e `PomodoroRing`), foco visível
(`--color-focus-ring`), labels para leitor de tela nos ícones de
`ContentBlockKind`.
