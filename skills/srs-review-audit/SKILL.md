---
name: srs-review-audit
description: Audita implementação e comportamento do algoritmo de repetição espaçada em packages/srs contra docs/specs/06-spaced-repetition.md. Use ao revisar mudanças em packages/srs ou ao investigar um agendamento de revisão que pareça errado.
---

# SRS Review Audit

## Quando usar

- Qualquer PR/mudança que toque `packages/srs`.
- Usuário relata "a revisão não fez sentido" (ex.: intervalo muito curto/longo,
  correção manual não refletiu na fila).

## Checklist de auditoria

1. **Casos de borda do algoritmo** (comparar com spec 06):
   - [ ] Primeira revisão (`repetitions: 0`) com `quality >= 3` → `intervalDays = 1`.
   - [ ] Segunda revisão consecutiva com sucesso → `intervalDays = 6`.
   - [ ] Terceira em diante → `intervalDays = round(intervalDays * easeFactor)`.
   - [ ] `quality < 3` a qualquer momento → `repetitions` zera e
         `intervalDays = 1`, mas `easeFactor` não desaba abaixo de `1.3`.
   - [ ] `easeFactor` nunca fica abaixo de `1.3` (piso do SM-2).
2. **Correção manual**:
   - [ ] "Ainda inseguro" aplica `quality = 2` e não deve ser tratado como
         "sucesso parcial" — deve reiniciar `repetitions`.
   - [ ] "Sei de cor" aplica `quality = 5` e avança o intervalo normalmente
         pela fórmula, não pula direto para um intervalo arbitrário grande.
   - [ ] `manualOverrideAt` é gravado e o histórico de `QuizAttempt` não é
         apagado nem alterado por uma correção manual.
3. **Sessão de Pomodoro sem quiz**:
   - [ ] Aplica `quality = 3` fixo, nunca conta como "sucesso forte"
         (não deve gerar o mesmo avanço de intervalo que um quiz com 95%).
4. **Integração com o Home** (spec 03):
   - [ ] `dueDate <= hoje` aparece corretamente na lista de revisão.
   - [ ] Ordenação é por `dueDate` ascendente, com `repetitions` como
         desempate.
5. **Cobertura de teste**: cada regra acima tem um teste unitário
   correspondente em `packages/srs` — se não tiver, escreva antes de aprovar
   a mudança.

## Sinal de alerta

Se uma mudança em `packages/srs` "conserta" um caso específico mas quebra um
dos itens acima, é sinal de que a correção foi feita ad-hoc em vez de revisar
a fórmula como um todo — pare e reavalie contra a spec, não contra o
sintoma isolado.
