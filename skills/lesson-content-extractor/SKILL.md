---
name: lesson-content-extractor
description: Converte docs/source/plano-didatico.md nas 42 lições estruturadas de packages/content, preservando fielmente o conteúdo (texto e código). Use sempre que docs/source/plano-didatico.md mudar, ou quando packages/content estiver vazio/desatualizado.
---

# Lesson Content Extractor

## Quando usar

- Primeira vez que `packages/content` precisa ser populado.
- Sempre que `docs/source/plano-didatico.md` for atualizado.
- Nunca para "melhorar" ou resumir conteúdo — só para refletir a fonte.

## Passo a passo

1. Leia `docs/source/plano-didatico.md` por completo.
2. Leia `docs/specs/02-content-pipeline.md` para as regras exatas de
   mapeamento ícone → `ContentBlockKind`.
3. Leia `docs/specs/01-data-model.md` para o schema `Lesson`/`ContentBlock`.
4. Para cada `# Aula N — Título`:
   - Identifique a `# PARTE` mais recente acima para `partId`.
   - Quebre o conteúdo em `ContentBlock[]` seguindo a tabela de mapeamento.
   - Preserve blocos de código (` ``` `) **verbatim**, incluindo linguagem
     do fence quando presente.
   - Preserve links de 📚 com texto e URL originais.
   - Atribua `theme` usando a lista de temas da tabela "Seu placar" (Parte
     XV do documento fonte) — nunca invente um tema novo sem checar essa
     lista primeiro.
5. Escreva `packages/content/src/lessons/aula-<NN>.json` (dois dígitos,
   `aula-01`..`aula-42`), validando contra o schema zod antes de salvar.
6. Atualize `packages/content/src/index.ts` se uma nova lição foi
   adicionada (não deveria acontecer fora de uma atualização de fonte).
7. Rode a checagem de integridade (spec 02): 42 lições, `order` 1..42 sem
   buracos, nenhum bloco vazio, contagem de fences de código bate com a
   fonte para aquela aula.
8. Gere um resumo do diff (quantas lições criadas/alteradas) para revisão
   humana — **não** faça commit automático de conteúdo sem esse resumo
   aparecer na conversa.

## Critério de sucesso

Reconstituir mentalmente uma aula a partir do JSON gerado deve dar
exatamente o mesmo texto e código da fonte — só a estrutura (blocos
tipados) muda, nunca o conteúdo.
