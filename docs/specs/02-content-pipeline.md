# 02 — Pipeline de conteúdo (MD → JSON fiel)

## Fonte

`docs/source/plano-didatico.md` (cópia intocada do documento original). Nunca
editar este arquivo para "ajustar" o app — qualquer diferença de conteúdo é
bug de extração, não motivo para editar a fonte.

## Saída

`packages/content/src/lessons/<lesson-id>.json`, validado contra o schema
`Lesson` de `packages/domain` (zod). Mais um `packages/content/src/index.ts`
que exporta `getAllLessons()`, `getLessonById()`, `getLessonsByPart()`.

## Regras de mapeamento

| Cabeçalho/ícone na fonte | `ContentBlock.kind` |
|---|---|
| 🧠 IDEIA | `idea` |
| 🤔 POR QUE ISSO EXISTE? | `why` |
| 🌎 CENÁRIO REAL | `scenario` |
| 🔍 INVESTIGAÇÃO / Perguntas | `investigation` |
| 🛠️ FERRAMENTA | `tool` |
| ⚖️ TRADE-OFF / Custo | `tradeoff` |
| 🚨 ARMADILHA / Cuidado | `trap` |
| 🎯 MISSÃO / Pergunta final | `mission` |
| 🧪 EXPERIMENTO | `experiment` |
| 💡 CURIOSIDADE | `curiosity` |
| 📚 APROFUNDAMENTO (links) | `reference` |

Aulas que não seguem o template completo (ex.: Aulas 12, 13, 16, os
"Architecture Challenges" da Parte IX) ainda devem ser quebradas em blocos
coerentes usando os `kind` mais próximos — o objetivo é granularidade útil
para exibição em cartões, não encaixar tudo à força no template das Aulas 1–11.

- **Blocos de código** (```` ``` ````) são preservados verbatim dentro do
  `markdown` do bloco correspondente — nunca reformatados ou "explicados" no
  lugar do original.
- Links de 📚 viram blocos `reference` com o texto e a URL preservados.
- Título da aula = texto após `# Aula N — ...`. `order` = N. `partId` vem do
  `# PARTE X` mais recente acima na fonte.
- `theme` de cada aula é atribuído manualmente (ver tabela da Parte XV do
  documento — "Design Patterns", "DDD", "Boundaries", "CQRS", etc.) porque a
  fonte não marca isso de forma extraível 1:1; use a tabela de temas do
  Placar como lista canônica de valores possíveis.

## Como isso é feito

Ver skill `skills/lesson-content-extractor/SKILL.md`. Ela é executada uma vez
para popular `packages/content`, e novamente sempre que `docs/source/plano-didatico.md`
mudar. Não é um passo de build automático — é deliberadamente um passo
supervisionado (revisão humana do diff do JSON gerado), porque fidelidade de
conteúdo é um requisito de produto, não um detalhe de engenharia.

## Validação de integridade

Um teste em `packages/content` garante:

- exatamente 42 lições presentes, `order` de 1 a 42 sem buracos;
- todo `Lesson.blocks` não-vazio;
- todo bloco de código na fonte para aquela aula aparece em algum
  `ContentBlock.markdown` da mesma aula (checagem best-effort por contagem de
  fences, para pegar perda grosseira de conteúdo).
