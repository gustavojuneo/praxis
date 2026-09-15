---
name: quiz-author
description: Redige ou revisa questões de quiz (multiple_choice, tradeoff, scenario) para uma lição, seguindo docs/specs/05-quiz-engine.md e o schema Question. Use ao criar quizzes para lições novas ou ao revisar qualidade de quizzes existentes em packages/content.
---

# Quiz Author

## Quando usar

- Uma lição em `packages/content` ainda não tem `packages/content/src/quizzes/aula-<NN>.json`.
- Revisão de qualidade de quizzes existentes (distratores fracos, explicação
  vaga, cenário sem tensão real entre as duas arquiteturas).

## Regras por tipo (ver spec 05 para detalhes completos)

### multiple_choice
- 4 opções, exatamente 1 correta.
- Distratores **plausíveis**: erros conceituais reais que alguém estudando
  aquela aula cometeria, não opções absurdas fáceis de eliminar por exclusão.
- `explanation` deve citar o motivo conceitual da aula, não só confirmar a
  opção certa.

### tradeoff
- Parta de uma solução concreta e específica (idealmente reaproveitando um
  cenário do próprio texto da aula, ex.: cache Redis, Strategy, extrair
  microservice).
- As opções erradas devem ser custos **reais de outras soluções conhecidas**
  do curso, para testar se o usuário sabe qual custo pertence a qual solução
  — não custos genéricos inventados.

### scenario
- Duas arquiteturas genuinamente competitivas para o mesmo cenário (nenhuma
  deve ser um "espantalho" obviamente pior).
- 3–5 `guidingQuestions` que forcem cobrir: requisito de negócio, trade-off
  técnico, e um failure mode.
- `rubric` com 4–6 itens objetivos e verificáveis pelo próprio usuário
  ("Mencionei o que acontece se a rede falhar no meio do fluxo?").

## Checklist de qualidade antes de salvar

- [ ] Toda questão referencia algo que está de fato no `ContentBlock` da
      lição (não invente fatos fora do material).
- [ ] Nenhuma opção correta é identificável só pelo tamanho/formatação do
      texto (erro clássico de quiz mal feito).
- [ ] `scenario` não tem "resposta certa" escondida na rubric — a rubric
      avalia qualidade do raciocínio, não uma escolha específica entre A/B.
- [ ] Validado contra o schema `Question` de `packages/domain`.
