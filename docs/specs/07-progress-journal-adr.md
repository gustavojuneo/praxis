# 07 — Diário de Engenharia e ADR

Implementa em produto a Aula 4 (ADR) e a Parte XII (Diário de Engenharia) do
próprio material — dogfooding direto.

## Modelo

Ver `JournalEntry` em `01-data-model.md`. Um único formulário serve tanto
para "ADR de uma decisão real do trabalho" (Missão da Aula 4/Semana 1) quanto
para entradas livres do diário — os campos são os mesmos template do material:
Situação, Primeira ideia, Perguntas que fiz, O que descobri, Alternativas,
Decisão, O que eu faria diferente.

## Onde aparece

- Rota `/diario`: lista cronológica (mais recente primeiro), com filtro
  opcional por aula vinculada.
- Botão "Registrar decisão" disponível a partir de qualquer tela de aula
  (pré-preenche `lessonId` e sugere o título "ADR — <título da aula>").
- Missões do tipo "resolver 2 decisões reais" (Semana 1) e "releia em
  dezembro" (Parte XII) têm atalho direto para criar/filtrar entradas.

## Sem correção automática

Diferente do quiz, entradas de diário não geram nota nem afetam o SRS
diretamente — é reflexão livre. Opcionalmente, ao salvar uma entrada
vinculada a uma aula, oferecer o botão de "Ajustar revisão" (spec 06) como
atalho, já que registrar uma decisão real é evidência forte de domínio.
