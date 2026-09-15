# 08 — Import / Export de progresso

## Por quê

Persistência é 100% local (IndexedDB). Sem backend, o usuário precisa poder
tirar uma cópia de segurança e mover entre dispositivos manualmente.

## Export

- Botão em `/configuracoes`: "Exportar progresso" gera um arquivo
  `senior-track-progress-YYYY-MM-DD.json` contendo `ProgressExport`
  (spec 01), baixado via `Blob` + link temporário (sem servidor envolvido).
- Conteúdo das aulas/quizzes **não** é incluído (vem com o app/versão).

## Import

- Botão "Importar progresso" aceita um arquivo `.json`.
- Validação com zod contra o schema `ProgressExport`; se `schemaVersion` for
  desconhecida ou o arquivo for inválido, mostrar erro claro sem tentar
  adivinhar o formato.
- Estratégia: **merge por padrão**, com opção explícita "substituir tudo":
  - Merge: `ReviewCard` por `lessonId` mais recente (`lastReviewedAt` maior)
    vence; `QuizAttempt`/`StudySession`/`JournalEntry` são concatenados e
    deduplicados por `id`; `ScoreboardEntry` por `(theme, month)` mais recente
    vence.
  - Substituir: apaga o estado local atual e grava exatamente o arquivo
    importado (com confirmação explícita, ação destrutiva).

## Preparação para o futuro backend

O mesmo schema `ProgressExport` é candidato natural a virar o payload de
sincronização quando existir API (spec 11) — import/export manual de hoje
vira "sync" automático depois, sem mudar o formato dos dados.
