# 05 — Motor de quiz

## Três tipos de questão (todos por aula, quando fizer sentido para o conteúdo)

### 1. Múltipla escolha, com feedback explicando o porquê

- 4 opções, 1 correta.
- Ao responder, revela imediatamente: ✅/❌ + `explanation`, que deve
  referenciar o motivo conceitual (não só "está certo/errado"), amarrado ao
  conteúdo daquela aula.
- Ex. baseado na Aula 6 (Strategy): "Por que Strategy só se justifica quando
  as regras de tributação mudam separadamente e são testadas isoladamente —
  não pela simples existência de um `if`."

### 2. Trade-off — dada uma solução, apontar o custo que ela cria

- Formato: apresenta uma solução concreta (ex.: "Adicionar Redis na
  listagem de produtos"), pede para escolher, entre as opções, **o custo
  real** que essa solução introduz (não o benefício).
- As opções erradas devem ser custos plausíveis de *outras* soluções, para
  forçar raciocínio específico, não eliminação óbvia.
- `explanation` sempre reforça o modelo mental da Aula 2 ("não existe
  solução grátis") citando o custo correto e por que os outros não se aplicam
  a esse caso.

### 3. Cenário — escolher entre 2 arquiteturas e justificar

- Apresenta um cenário (pode reaproveitar os Architecture Challenges da
  Parte IX) e duas arquiteturas plausíveis (`architectureA`/`architectureB`).
- Usuário escreve uma justificativa em texto livre respondendo às
  `guidingQuestions`.
- **Não há gabarito único.** Correção é uma autoavaliação guiada: depois de
  escrever, o app mostra a `rubric` (checklist do que uma boa resposta sênior
  cobriria — nos moldes da Parte XIII) e o usuário se autoavalia de 1 a 5.
  Esse `selfRating` alimenta o SRS como se fosse a "nota" da questão (ver
  spec 06).

## Fluxo do quiz runner

```
Selecionar aula com quiz pendente
        ↓
Para cada questão (ordem fixa: multiple_choice → tradeoff → scenario)
        ↓
  Responder → feedback imediato → próxima
        ↓
Tela de resultado: % de acerto objetivo (MC + tradeoff) + rating do cenário
        ↓
Gravar QuizAttempt
        ↓
Atualizar ReviewCard via packages/srs (spec 06)
```

- Quiz pode ser refeito quantas vezes o usuário quiser; cada tentativa gera
  um novo `QuizAttempt` — histórico completo é mantido (útil para o Diário e
  para ver evolução).
- Sem tempo limite por questão — o produto é sobre entendimento, não
  velocidade (o Pomodoro já cuida do tempo).

## Cálculo de `scorePercent`

`scorePercent = acertos(multiple_choice + tradeoff) / total(multiple_choice + tradeoff) * 100`.
Questões `scenario` não entram nesse percentual (são qualitativas), mas o
`selfRating` médio delas é mostrado separadamente na tela de resultado.
