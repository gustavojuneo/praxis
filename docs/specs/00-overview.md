# 00 — Visão geral

## Problema

O plano didático "Engenharia Sênior na Prática" é excelente em conteúdo, mas
é um documento estático de 42 aulas. Documentos estáticos morrem na aba do
navegador. Falta:

- um lugar que diga **o que estudar hoje**, sem o usuário decidir toda vez;
- um mecanismo de **foco com pausas** (Pomodoro) para as sessões de 20–90 min
  descritas na Parte XI (Rotina Semanal);
- uma forma de **testar se o conteúdo realmente entrou**, não só "li";
- um sistema de **repetição espaçada** para que aulas de setembro não sejam
  esquecidas em dezembro.

## Objetivo do produto

Transformar as 42 aulas em uma prática diária de ~20–90 minutos que:

1. decide por você a sessão do dia (revisão pendente + próxima aula nova);
2. roda um Pomodoro vinculado ao assunto, registrando tempo acumulado;
3. avalia aprendizado com 3 tipos de questão (múltipla escolha, trade-off,
   cenário);
4. agenda revisões futuras com repetição espaçada, ajustável manualmente;
5. preserva o conteúdo das 42 aulas na íntegra — nada é resumido.

## Não-objetivos (por agora)

- Múltiplos usuários / autenticação / backend (ver `11-roadmap-saas.md` para
  o caminho quando isso for necessário).
- Geração automática de novas aulas por IA dentro do app (o conteúdo é fixo,
  vindo do plano didático).
- Gamificação pesada (streaks, badges) além do placar já previsto no próprio
  material (Parte XV).

## Plataformas

Web responsivo, com prioridade igual para mobile e desktop (uso tanto no
celular em momentos curtos quanto no desktop nas sessões de sábado de 90 min).
Um único app React responsivo — sem necessidade de app nativo agora.

## Princípio de fidelidade de conteúdo

O material tem blocos com significado pedagógico específico (🧠 Ideia, 🤔 Por
quê, 🌎 Cenário, 🔍 Investigação, 🛠️ Ferramenta, ⚖️ Trade-off, 🚨 Armadilha,
🎯 Missão, 🧪 Experimento, 💡 Curiosidade, 📚 Aprofundamento). Esses blocos são
a unidade de conteúdo do app — cada um vira um "cartão" visualmente distinto
na tela de aula (ver spec 10). Nenhuma aula pode ser exibida incompleta.

## Métricas de sucesso (autoavaliação, não telemetria de produto)

- As 42 aulas revisadas ao menos uma vez até dezembro.
- Placar da Parte XV preenchido mês a mês (Set/Out/Nov/Dez) por tema.
- Nenhuma revisão SRS "vencida" há mais de 7 dias sem ação (indicador de
  abandono do hábito, mostrado no Home).
