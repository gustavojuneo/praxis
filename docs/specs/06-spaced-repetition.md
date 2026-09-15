# 06 — Repetição espaçada (SRS)

Vive em `packages/srs`, puro TypeScript, sem UI, 100% testável. Baseado em
SM-2 (mesma família do Anki), com dois pontos de entrada: **automático** (via
quiz) e **manual** (correção do usuário).

## Mapeamento de desempenho → qualidade (0–5)

| Origem | Regra |
|---|---|
| Quiz (multiple_choice + tradeoff) | `scorePercent >= 90 → 5`, `>= 75 → 4`, `>= 50 → 3`, `>= 25 → 2`, `else → 1` |
| Quiz (scenario, `selfRating`) | usado diretamente como qualidade (1–5) e combinado por média simples com a qualidade objetiva quando ambos existem |
| Sessão de Pomodoro sem quiz (só leitura/estudo) | qualidade fixa `3` ("revisado, sem teste") — não avança tanto quanto um quiz bem-sucedido, mas evita que a aula fique "presa" sem nenhuma revisão registrada |
| Correção manual do usuário | usuário escolhe diretamente entre "Ainda inseguro" (`quality = 2`) ou "Sei de cor" (`quality = 5`) a qualquer momento, fora do fluxo de quiz |

## Algoritmo (SM-2 adaptado)

```ts
function schedule(card: ReviewCard, quality: 0|1|2|3|4|5, now: Date): ReviewCard {
  const q = quality;
  let { easeFactor, intervalDays, repetitions } = card;

  if (q < 3) {
    // errou/inseguro: reinicia a progressão, mas não zera a ease abruptamente
    repetitions = 0;
    intervalDays = 1;
  } else {
    repetitions += 1;
    intervalDays =
      repetitions === 1 ? 1 :
      repetitions === 2 ? 6 :
      Math.round(intervalDays * easeFactor);
  }

  easeFactor = Math.max(
    1.3,
    easeFactor + (0.1 - (5 - q) * (0.08 + (5 - q) * 0.02)),
  );

  const dueDate = addDays(now, intervalDays);
  return { ...card, easeFactor, intervalDays, repetitions, dueDate: dueDate.toISOString(), lastQuality: q, lastReviewedAt: now.toISOString() };
}
```

- `easeFactor` inicial: `2.5`. `intervalDays` inicial: `0` (nunca revisado →
  `dueDate` = hoje, para entrar na lista de "sessão de hoje" assim que a
  primeira `QuizAttempt` ou `StudySession` da aula existir).
- Toda aula nova ganha um `ReviewCard` no momento em que é aberta pela
  primeira vez (não espera o quiz para existir o card — assim ela já aparece
  corretamente no fluxo do Home mesmo antes de qualquer avaliação).

## Correção manual — UI e regra

Na tela de qualquer aula (ou na fila de revisão), botão secundário
"Ajustar revisão":

```
┌─────────────────────────────┐
│ Como você avalia seu domínio │
│ deste assunto agora?         │
│                               │
│  [Ainda inseguro]  [Sei de cor] │
└─────────────────────────────┘
```

- Aplica `schedule(card, quality, now)` com `quality = 2` ou `5`.
- Marca `manualOverrideAt = now`.
- **Não é destrutivo**: não apaga histórico de `QuizAttempt`; só reagenda o
  `ReviewCard`. Isso mantém a promessa do requisito ("prova ajusta, mas eu
  posso corrigir manualmente") sem esconder o histórico real de desempenho.

## Regras de exibição no Home

- `dueDate <= hoje` → aparece em "Revisar" (spec 03).
- Ordenação por `dueDate` ascendente (mais atrasado primeiro), com
  `repetitions` baixo como desempate (prioriza o que está menos consolidado).
