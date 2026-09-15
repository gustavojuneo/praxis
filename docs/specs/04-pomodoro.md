# 04 — Pomodoro

## Objetivo

Rodar blocos de foco cronometrados vinculados a uma aula, com pausas, som ao
final, e registro de tempo acumulado por assunto/tema (para o resumo do Home
e para o Placar).

## Máquina de estados

```
idle → focus → (completa) → short_break → focus → ... → (a cada 4 focos) → long_break
                  ↓ pausar/cancelar
                idle
```

- `focus`: padrão 25 min (configurável em `configuracoes`, 15–50 min).
- `short_break`: padrão 5 min.
- `long_break`: padrão 15 min, a cada 4 blocos de foco completados em sequência.
- Pausar não é "parar o relógio" silenciosamente — pausar guarda o tempo já
  decorrido e o estado fica visível como "pausado", exige ação explícita para
  retomar ou cancelar.

## Implementação do timer

- Guardar `endTimestamp = Date.now() + remainingMs` ao iniciar/retomar.
  Renderizar `remaining = endTimestamp - Date.now()` a cada tick (ex.
  `requestAnimationFrame` ou `setInterval(1000)` só para re-render, nunca para
  contar). Isso evita drift e funciona corretamente após o tab voltar do
  background.
- Ao detectar `visibilitychange` voltando para visível, recalcular o tempo
  restante imediatamente (não esperar o próximo tick).

## Vínculo com a aula e registro

- Todo bloco `focus` iniciado a partir do Home ou da tela de aula é criado
  como `StudySession { lessonId, kind: "focus", plannedDurationSec, ... }`.
- Ao completar (ou ao cancelar com >30s decorridos), grava
  `actualDurationSec` e `completed`.
- Sessões livres (sem aula, iniciadas da tela de Configurações ou de um
  atalho genérico) usam `lessonId: null` e não contam para o tempo por tema.

## Som / aviso ao fim do bloco

- Toca um som curto (asset local, sem depender de rede) ao fim de `focus` e
  de `break`.
- Primeira interação do usuário no app "destrava" o áudio (regra dos
  navegadores para autoplay) — mostrar isso de forma transparente na primeira
  vez que o Pomodoro é usado ("toque em Iniciar para ativar o som").
- Fallback: se o áudio falhar (bloqueado, aba sem foco), usar a
  Notification API (se permissão concedida) como aviso visual complementar —
  nunca só isso, o som é o mecanismo principal.

## Exibição de tempo acumulado

Na tela de conteúdo da aula (e no Home), mostrar:

```
⏱️ 1h 20min estudados nesta aula · 4h 05min no tema "Design Patterns"
```

Cálculo: soma de `actualDurationSec` de `StudySession` com `completed: true`
e `kind: "focus"`, filtrado por `lessonId` (para o total da aula) e por
`Lesson.theme` (para o total do tema) — via `packages/domain` helper puro,
testável sem UI.

## UI mínima do widget

```
┌───────────────────────────┐
│        Aula 22             │
│                             │
│         ⏺ 18:24             │
│      (anel de progresso)   │
│                             │
│  [Pausar]   [Pular pausa]  │
└───────────────────────────┘
```
