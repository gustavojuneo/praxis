# 09 — Navegação e arquitetura de informação

## Mapa de rotas (TanStack Router)

```
/                      Home — Sessão de hoje (spec 03)
/trilha                Lista de Partes → Aulas (visão geral do curso)
/trilha/$lessonId      Conteúdo completo da aula + Pomodoro inline + CTA quiz
/quiz/$lessonId        Runner de quiz (spec 05)
/revisao               Fila de revisão SRS — todas as pendentes, não só as do Home
/diario                Diário de Engenharia / ADRs (spec 07)
/placar                Scoreboard da Parte XV (grid Tema × Mês × nível 0–6)
/configuracoes         Pomodoro (durações, som), export/import (spec 08), tema claro/escuro
```

## Navegação principal

- **Mobile:** bottom tab bar com 5 destinos: Hoje, Trilha, Revisão, Diário, Mais
  (Mais → Placar + Configurações).
- **Desktop:** sidebar fixa à esquerda com os mesmos destinos, sempre visível.
- Mesma hierarquia de informação nos dois — só muda o container de navegação
  (bottom bar vs. sidebar), reaproveitando os mesmos componentes de item de
  menu (ver spec 10).

## Regras de link entre telas

- Toda aula em `/trilha/$lessonId` mostra, se existir, o `ReviewCard` (due
  date, nível) e o histórico resumido de `QuizAttempt`.
- Cartão de revisão no Home e em `/revisao` leva direto para
  `/trilha/$lessonId` com o Pomodoro pré-armado, não direto para o quiz —
  revisão de conteúdo antes de testar de novo é o padrão; o quiz é uma ação
  explícita a partir da aula.
