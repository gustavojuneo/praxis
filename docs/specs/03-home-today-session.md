# 03 — Home: "Sessão de hoje"

## Objetivo

O usuário abre o app e em menos de 3 segundos sabe **exatamente** o que fazer
agora, sem decidir nada. É a tela mais importante do produto.

## Algoritmo de composição da sessão do dia

Executa client-side, ao abrir o Home, na ordem:

1. **Revisões vencidas** — todo `ReviewCard` com `dueDate <= hoje`, ordenado
   pela mais atrasada primeiro. Se houver mais de 5, mostrar 5 e um contador
   "+N pendentes" (evita sobrecarregar o usuário num dia ruim).
2. **Próxima aula nova** — a primeira `Lesson` (por `order`) que ainda não
   tem nenhum `QuizAttempt` nem `StudySession` associada. Segue a ordem do
   cronograma da Parte X (que já é a ordem natural de `order` 1–42).
3. Se não houver nada pendente (revisões em dia e aula do dia já feita),
   mostrar estado de "dia concluído" com opção de estudar adiantado ("Puxar
   próxima aula mesmo assim") ou revisar algo à escolha.

## Layout do Home

```
┌─────────────────────────────────────────┐
│  Sessão de hoje                          │
│                                           │
│  ⏰ Revisar (2 pendentes)                 │
│    • Aula 08 — Adapter        [Revisar]  │
│    • Aula 15 — Aggregate      [Revisar]  │
│                                           │
│  📘 Próxima aula                          │
│    Aula 22 — A investigação de uma query │
│    [Começar Pomodoro]  [Ver conteúdo]     │
│                                           │
│  ── resumo rápido ──                      │
│  🔥 tempo estudado hoje: 25 min           │
│  📅 3 de 42 aulas revisadas esta semana   │
└─────────────────────────────────────────┘
```

- Botão primário sempre é **"Começar Pomodoro"**, pré-vinculado à
  primeira revisão pendente (ou à próxima aula nova se não houver revisão).
  Clicar abre a tela de conteúdo da aula já com o Pomodoro rodando (spec 04).
- "Ver conteúdo" abre a aula sem iniciar timer (para quem só quer ler).
- Cartões de revisão têm indicador visual de atraso (0 dias = normal, 1–3 =
  atenção, 4+ = destaque, sem culpar o usuário no texto — tom sempre neutro).

## Estado vazio (primeiro uso)

Sem nenhum `ReviewCard` e sem `StudySession`: Home mostra direto a Aula 1 com
CTA "Começar sua primeira sessão" e um resumo de 3 linhas de como o app
funciona (Pomodoro → conteúdo → quiz → revisão volta sozinha).
