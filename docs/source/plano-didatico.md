# Engenharia Sênior na Prática

## Um plano intensivo e didático de setembro a dezembro

### C#/.NET · React · React Native · Arquitetura · Design Patterns · System Design · Design Systems

> **Objetivo deste material:** não transformar você em uma enciclopédia
> de patterns.\
> O objetivo é treinar uma habilidade muito mais valiosa:
>
> **olhar para um problema, fazer as perguntas certas, enxergar
> consequências, comparar soluções e defender tecnicamente uma
> decisão.**

------------------------------------------------------------------------

# Como esta aula funciona

Você não vai encontrar capítulos enormes seguidos de "agora pratique".

Cada assunto segue este formato:

``` text
🧠 IDEIA
O conceito explicado de forma curta.

🤔 POR QUE ISSO EXISTE?
O problema que levou alguém a criar essa solução.

🌎 CENÁRIO REAL
Uma situação parecida com algo que aparece em sistemas reais.

🔍 INVESTIGAÇÃO
Perguntas que um engenheiro experiente faria.

🛠️ FERRAMENTA
Pattern/técnica que pode resolver o problema.

⚖️ TRADE-OFF
O preço que pagamos pela solução.

🚨 ARMADILHA
Quando essa solução vira overengineering.

🎯 MISSÃO
Um exercício curto.

🧪 EXPERIMENTO
Algo que você pode quebrar, medir ou comparar.

💡 CURIOSIDADE
Uma conexão com outro conceito para alimentar a curiosidade.

📚 APROFUNDAMENTO
Uma referência selecionada — opcional depois que a aula estiver compreendida.
```

## Uma regra importante

Você **não precisa terminar uma aula por sessão**.

Uma sessão pode ser apenas:

``` text
20 minutos
→ entender uma ideia
→ responder uma pergunta
→ fechar o material
```

No dia seguinte você continua.

------------------------------------------------------------------------

# PARTE I --- COMO UM SÊNIOR PENSA

# Aula 1 --- O trabalho real não é escolher tecnologia

Imagine esta conversa:

> **Dev:** "A listagem está lenta. Acho que precisamos colocar Redis."

Parece razoável.

Mas observe a diferença.

### Pensamento orientado à solução

``` text
Problema: endpoint lento
Solução: Redis
```

### Pensamento orientado à investigação

``` text
Endpoint lento
     ↓
Quanto?
     ↓
Onde está o tempo?
     ↓
Por quê?
     ↓
Quais alternativas?
     ↓
Qual resolve com menor custo?
```

Isso muda tudo.

## 🌎 Cenário real

A API:

``` text
GET /products
P95 = 920 ms
```

Alguém sugere cache.

Você mede:

``` text
PostgreSQL        730 ms
Serialização       40 ms
Aplicação           50 ms
Rede               100 ms
```

Agora você olha a query.

``` sql
SELECT *
FROM products
WHERE company_id = 42
AND active = true;
```

O execution plan mostra uma varredura enorme.

Você cria o índice adequado.

Novo resultado:

``` text
PostgreSQL         35 ms
P95 total         180 ms
```

Você resolveu o problema **sem Redis**.

## 🧠 Lição

Um engenheiro experiente não é aquele que conhece mais ferramentas.

É aquele que consegue evitar ferramentas desnecessárias.

## 🔍 Perguntas

Quando alguém disser:

> "Precisamos de X."

pergunte mentalmente:

``` text
1. Qual problema X está tentando resolver?
2. Temos evidência de que esse problema existe?
3. Qual é o impacto?
4. Existe solução mais simples?
5. Qual complexidade X adiciona?
6. Como vamos saber se funcionou?
```

## 🎯 Missão

Pegue uma decisão recente do trabalho.

Preencha:

``` text
Problema:
Evidência:
Solução escolhida:
Alternativas:
Custo da solução:
Como eu mediria o resultado:
```

------------------------------------------------------------------------

# Aula 2 --- Toda arquitetura é uma troca

Existe uma frase que você deve internalizar:

> **Não existe solução grátis.**

Quer cache?

Você ganha velocidade.

Também ganha:

``` text
invalidação
stale data
mais infraestrutura
mais failure modes
```

Quer microservices?

Você ganha:

``` text
deploy independente
isolamento
escala independente
```

Também ganha:

``` text
rede
latência
mensageria
consistência eventual
observabilidade distribuída
versionamento de contratos
mais DevOps
```

Quer abstração?

Você ganha flexibilidade.

Também ganha indireção.

## 🧠 Modelo mental

``` text
DECISÃO
  ├── resolve problema A
  ├── melhora atributo B
  └── cria custos C, D e E
```

A pergunta de um sênior não é:

> "Isso é bom?"

É:

> **"Esse benefício vale esse custo neste contexto?"**

------------------------------------------------------------------------

# Aula 3 --- Fatos, hipóteses e opiniões

Compare:

> "Kafka é melhor."

com:

> "Precisamos sustentar 30 mil eventos/s, retenção para replay e
> múltiplos consumidores independentes. Vamos comparar Kafka e RabbitMQ
> nesses critérios."

A segunda afirmação possui critérios.

## Classifique suas informações

### Fato

``` text
P95 = 780 ms nas últimas 24h.
```

### Hipótese

``` text
A maior parte da latência provavelmente vem do banco.
```

### Opinião

``` text
Eu prefiro Redis.
```

### Restrição

``` text
Precisamos entregar em duas semanas e a equipe nunca operou Kafka.
```

Não trate hipótese como fato.

## 🎯 Missão

Durante uma discussão técnica real, anote:

``` text
F:
H:
O:
R:
```

Tente separar o que está sendo dito nas quatro categorias.

------------------------------------------------------------------------

# Aula 4 --- ADR: memória das decisões

Daqui a seis meses alguém perguntará:

> "Por que fizemos isso?"

Git mostra **o que** mudou.

ADR registra **por quê**.

Use:

``` markdown
# ADR-007 — Manter Billing no Modular Monolith

## Contexto
...

## Problema
...

## Alternativas
1. manter módulo
2. extrair microservice

## Evidências
...

## Decisão
...

## Trade-offs
...

## Como validar
...
```

Não escreva uma tese.

Uma boa decisão pode caber em uma página.

📚
https://learn.microsoft.com/en-us/azure/well-architected/architect-role/architecture-decision-record

------------------------------------------------------------------------

# PARTE II --- DESIGN PATTERNS SEM DECORAÇÃO

# Aula 5 --- O segredo para aprender patterns

Não pense:

> "Strategy tem uma interface e ConcreteStrategies."

Pense:

> **"Tenho um comportamento que muda."**

Patterns são nomes dados a formas recorrentes de resolver problemas
recorrentes.

O nome serve para comprimir comunicação.

Quando você diz:

> "Podemos usar Adapter aqui."

outro desenvolvedor experiente já imagina:

``` text
contrato A
   ↓
tradução
   ↓
contrato B
```

O valor está nessa linguagem compartilhada.

📚 Catálogo visual: https://refactoring.guru/design-patterns

------------------------------------------------------------------------

# Aula 6 --- Strategy: "o algoritmo muda"

## 🌎 Cenário

Sistema fiscal:

``` csharp
if (tipo == Venda)
{
    // 30 linhas
}
else if (tipo == Devolucao)
{
    // 45 linhas
}
else if (tipo == Transferencia)
{
    // 28 linhas
}
```

Ter `if` não é o problema.

Pergunte:

``` text
Essas regras mudam separadamente?
Novos tipos aparecem?
Cada regra é complexa?
Precisamos testá-las isoladamente?
```

Se sim, existe uma dimensão clara de variação.

## 🛠️ Strategy

``` text
Calculador
    ↓
ICalculoStrategy
    ├── VendaStrategy
    ├── DevolucaoStrategy
    └── TransferenciaStrategy
```

## ⚖️ Custo

Antes:

``` text
1 arquivo
1 switch
```

Depois:

``` text
interface
3 classes
seleção da strategy
DI
mais navegação
```

Strategy só venceu se essa complexidade comprou algo útil.

## 🚨 Não use porque...

...existem dois `if`s simples e estáveis.

## 💡 Curiosidade

Strategy e State podem ter diagramas parecidos.

A diferença está na **intenção**:

``` text
Strategy → escolho um algoritmo
State    → objeto muda comportamento conforme seu estado
```

## 🎯 Missão

Explique sem usar a palavra Strategy:

> "Qual problema esse pattern resolve?"

Se conseguir, aprendeu a parte importante.

📚 https://refactoring.guru/design-patterns/strategy

------------------------------------------------------------------------

# Aula 7 --- Factory: "quem cria não deveria conhecer tudo"

Factory não significa:

``` text
Todo new é ruim.
```

Imagine:

``` csharp
new PaymentClient(
    credentials,
    timeout,
    serializer,
    retryPolicy,
    environment,
    telemetry);
```

E a configuração depende do provedor.

Quem só quer cobrar um pagamento não deveria precisar conhecer toda essa
construção.

## 🛠️ Factory

Centraliza uma criação que possui variação ou regras relevantes.

## 🚨 Armadilha

``` csharp
class UserFactory
{
    User Create() => new User();
}
```

Se isso não protege regra, não esconde complexidade e não oferece
variação, talvez só tenha criado um arquivo extra.

📚 https://refactoring.guru/design-patterns/factory-method

------------------------------------------------------------------------

# Aula 8 --- Adapter: "não deixe o mundo externo falar dentro do domínio"

Seu sistema pensa:

``` text
Pagamento aprovado
```

Gateway X pensa:

``` text
CAPTURED
```

Gateway Y pensa:

``` text
SETTLED
```

Seu domínio não deveria ficar cheio de:

``` csharp
if (providerStatus == "CAPTURED" ...)
```

## 🛠️ Adapter

``` text
Seu sistema
     ↓
IPaymentGateway
     ↑
GatewayXAdapter
     ↓
SDK externo
```

O Adapter cria uma fronteira de tradução.

## 🔍 Pergunta de sênior

> Se o fornecedor mudar completamente amanhã, quanto do meu sistema será
> afetado?

📚 https://refactoring.guru/design-patterns/adapter

------------------------------------------------------------------------

# Aula 9 --- Decorator: "quero adicionar comportamento sem misturar responsabilidades"

Imagine:

``` csharp
ProcessPayment()
{
    Log();
    StartMetric();
    Authorize();
    Retry();
    // negócio
    SaveMetric();
    Log();
}
```

O negócio desapareceu no meio de preocupações transversais.

Decorator permite compor:

``` text
Logging
   ↓
Metrics
   ↓
Authorization
   ↓
PaymentService
```

## ⚖️ Trade-off

Composição é poderosa.

Mas dez decorators podem transformar debugging em caça ao tesouro.

📚 https://refactoring.guru/design-patterns/decorator

------------------------------------------------------------------------

# Aula 10 --- State: "o comportamento depende do estado atual"

Pedido:

``` text
Draft
  ↓
PendingPayment
  ↓
Paid
  ↓
Shipped
```

Regras:

``` text
Draft pode editar.
Paid não pode editar.
Shipped não pode cancelar.
PendingPayment pode expirar.
```

Primeiro encapsule:

``` csharp
order.Cancel();
order.Pay();
order.Ship();
```

Se a máquina de estados crescer muito, State pode tornar cada
comportamento explícito.

## 🚨 Regra

**Enum não implica State Pattern.**

Complexidade comportamental é que pode justificá-lo.

📚 https://refactoring.guru/design-patterns/state

------------------------------------------------------------------------

# Aula 11 --- Chain of Responsibility: "isso é um pipeline?"

Imagine:

``` text
Request
  ↓
Authentication
  ↓
Authorization
  ↓
Validation
  ↓
Logging
  ↓
Handler
```

Cada etapa:

``` text
recebe
processa
continua OU interrompe
```

Esse formato aparece naturalmente em middleware.

Também pode aparecer em:

``` text
validações
processamento de documentos
regras sequenciais
```

## 🚨 Cuidado

Fluxos de negócio importantes escondidos em 14 handlers podem ficar
difíceis de compreender.

📚 https://refactoring.guru/design-patterns/chain-of-responsibility

------------------------------------------------------------------------

# Aula 12 --- Seu mapa rápido de patterns

  Quando você pensa...                             Investigue
  ------------------------------------------------ -------------------------
  "o algoritmo varia"                              Strategy
  "a criação varia/é complexa"                     Factory / Builder
  "preciso traduzir contratos"                     Adapter
  "quero adicionar comportamento por composição"   Decorator
  "quero esconder um subsistema complexo"          Facade
  "o comportamento muda com o estado"              State
  "a solicitação precisa virar um objeto"          Command
  "tenho um pipeline de handlers"                  Chain of Responsibility
  "vários interessados reagem a algo"              Observer
  "todo mundo conhece todo mundo"                  Mediator
  "condições complexas precisam ser compostas"     Specification

## ⚠️ Não use esta tabela ao contrário

Errado:

``` text
Ainda não usei Decorator.
Onde consigo colocar um?
```

Certo:

``` text
Tenho este problema.
Existe um pattern conhecido que ajuda?
```

------------------------------------------------------------------------

# PARTE III --- DDD: MODELAR O NEGÓCIO, NÃO AS TABELAS

# Aula 13 --- Bounded Context

A palavra `Cliente` parece óbvia.

Mas:

``` text
CRM
Cliente = lead, contatos, oportunidades

Financeiro
Cliente = crédito, cobrança, inadimplência

Suporte
Cliente = tickets, SLA, histórico

Fiscal
Cliente = dados tributários
```

Mesma pessoa.

Modelos diferentes.

## 🧠 Ideia

Um Bounded Context define onde determinado modelo e linguagem possuem
significado consistente.

## 🎯 Missão

Escolha uma palavra importante do sistema em que trabalha.

Pergunte:

``` text
Ela significa exatamente a mesma coisa em todos os módulos?
Quem é dono desse conceito?
Quais propriedades só existem em determinados contextos?
```

📚
https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis

------------------------------------------------------------------------

# Aula 14 --- Entity e Value Object

## Entity

Identidade importa.

``` text
Pedido 7 continua sendo Pedido 7
mesmo depois que status e endereço mudarem.
```

## Value Object

Valor importa.

``` csharp
Money(100, "BRL")
Cnpj("...")
DateRange(start, end)
```

Value Objects permitem mover regras para perto dos dados.

Em vez de:

``` csharp
if (cnpj.Length != 14) ...
```

espalhado pelo sistema:

``` csharp
Cnpj.Create(...)
```

centraliza a invariância.

------------------------------------------------------------------------

# Aula 15 --- Aggregate: uma fronteira de consistência

Essa frase merece atenção:

> **Aggregate é uma boundary de consistência, não uma árvore de
> objetos.**

Pedido:

``` text
Order
 ├── Items
 └── Address
```

Talvez seja obrigatório:

``` text
Order.Total == soma dos itens
```

dentro da mesma transação.

Mas será que `Inventory` precisa estar no mesmo Aggregate?

Provavelmente não.

## 🔍 Pergunta

> O que realmente precisa ficar consistente imediatamente?

Quanto maior o Aggregate:

``` text
mais contenção
mais conflitos
mais dados
mais locking
```

------------------------------------------------------------------------

# Aula 16 --- Command vs Event

``` text
COMMAND
ReserveStock

EVENT
StockReserved
```

Command:

> "Faça isso."

Event:

> "Isso aconteceu."

Uma diferença simples que evita muita confusão arquitetural.

------------------------------------------------------------------------

# PARTE IV --- ESTRUTURA DO SISTEMA

# Aula 17 --- Clean e Hexagonal: a ideia por trás dos círculos

Ignore os diagramas por um minuto.

Pergunte:

> Se amanhã eu trocar PostgreSQL por outra persistência, minhas regras
> de negócio precisam mudar?

Idealmente:

``` text
Domínio
   ↑
Aplicação
   ↑
Infraestrutura
```

As políticas centrais não dependem dos detalhes externos.

## 🚨 Cerimônia arquitetural

Isso não significa criar:

``` text
IService
Service
IRepository
Repository
IFactory
Factory
```

para tudo.

A direção de dependência importa mais que a quantidade de projetos
`.csproj`.

📚
https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures

------------------------------------------------------------------------

# Aula 18 --- Modular Monolith: simplicidade é uma feature

Temos:

``` text
Catalog
Orders
Inventory
Billing
```

Podemos criar quatro microservices.

Mas por quê?

Se estiverem no mesmo processo com boundaries explícitas:

``` text
Modules/
  Orders/
  Billing/
  Inventory/
  Catalog/
```

podemos obter boa modularidade com:

``` text
debug local simples
deploy único
transações locais
menos infraestrutura
```

## 🧠 Insight

Monólito não significa necessariamente "Big Ball of Mud".

Um monólito pode ser modular.

Um conjunto de microservices pode ser um **distributed monolith**.

## 🎯 Pergunta

> Se eu separar esses módulos em processos diferentes, qual problema
> concreto passa a ser resolvido?

📚 https://microservices.io/patterns/monolithic.html\
📚 https://microservices.io/patterns/microservices.html

------------------------------------------------------------------------

# Aula 19 --- Vertical Slice: organize por mudança

Horizontal:

``` text
Controllers
Services
Repositories
Validators
```

Vertical:

``` text
Orders/
  Create/
  Cancel/
  List/
```

Pergunte:

> O que costuma mudar junto?

Vertical Slice tenta aproximar código que participa do mesmo caso de
uso.

Isso não elimina camadas conceituais.

Ele muda principalmente a **unidade de organização**.

------------------------------------------------------------------------

# Aula 20 --- CQRS sem exagero

CQRS começa com uma ideia pequena:

``` text
WRITE MODEL ≠ READ MODEL
```

Talvez escrever um pedido precise de:

``` text
Aggregate
invariants
transaction
```

Enquanto a tela "Meus Pedidos" precisa apenas:

``` sql
SELECT id, date, status, total
```

Não há obrigação de reconstruir Aggregate para ler uma lista.

## 🚨 CQRS não exige

``` text
Event Sourcing
Kafka
dois bancos
microservices
```

## Pergunta

> Leitura e escrita possuem necessidades suficientemente diferentes para
> justificar modelos separados?

📚 https://martinfowler.com/bliki/CQRS.html

------------------------------------------------------------------------

# PARTE V --- PERFORMANCE E DADOS

# Aula 21 --- Média pode mentir

Tempos:

``` text
50
55
52
48
54
60
51
49
55
2000 ms
```

A maioria é rápida.

Um usuário teve experiência terrível.

Por isso observe:

``` text
P50
P95
P99
```

E defina SLO:

``` text
95% das requisições < 250 ms
```

Agora "rápido" possui significado.

------------------------------------------------------------------------

# Aula 22 --- A investigação de uma query

Antes de cache:

``` text
1. SQL real
2. execution plan
3. índices
4. cardinalidade
5. N+1
6. projection
7. round trips
8. medir novamente
```

## 🧪 Experimento

Crie 100 mil registros.

Execute uma consulta sem índice.

Registre:

``` text
tempo:
plan:
rows scanned:
```

Adicione o índice correto.

Repita.

Essa experiência ensina mais que decorar "índices melhoram performance".

📚
https://learn.microsoft.com/en-us/ef/core/performance/efficient-querying

------------------------------------------------------------------------

# Aula 23 --- Concorrência: duas verdades ao mesmo tempo

``` text
Estoque = 1

A lê 1
B lê 1

A compra
B compra
```

As duas requisições estavam corretas quando leram.

O estado mudou entre leitura e escrita.

Investigue:

``` text
optimistic concurrency
atomic update
locking
reservation
queue
```

A solução depende do negócio.

Um ingresso para final de campeonato e uma quantidade aproximada de
likes possuem necessidades de consistência muito diferentes.

------------------------------------------------------------------------

# PARTE VI --- SISTEMAS DISTRIBUÍDOS

# Aula 24 --- A rede transforma chamadas em incerteza

Dentro do processo:

``` csharp
result = calculator.Calculate();
```

Ou retorna ou lança erro.

Na rede:

``` text
Você envia a requisição.
Timeout.
```

O que aconteceu?

Talvez:

``` text
A) servidor nunca recebeu
B) recebeu e falhou
C) processou e resposta se perdeu
D) ainda está processando
```

Você **não sabe**.

Essa incerteza explica vários patterns distribuídos.

------------------------------------------------------------------------

# Aula 25 --- Database per Service

Se `Orders` e `Billing` são serviços independentes, permitir:

``` text
Orders → UPDATE BillingDatabase
```

quebra a autonomia.

Database per Service diz que cada serviço possui seus dados.

## Você ganhou

``` text
autonomia
encapsulamento
evolução independente
```

## Você perdeu

``` text
JOIN simples
transação ACID entre módulos
consultas centralizadas
```

Agora surgem novos problemas.

📚 https://microservices.io/patterns/data/database-per-service.html

------------------------------------------------------------------------

# Aula 26 --- Saga: uma transação virou uma história

Fluxo:

``` text
Create Order
    ↓
Reserve Stock
    ↓
Charge Payment
    ↓
Confirm
```

Cada passo possui sua transação.

Payment falhou.

Não existe:

``` sql
ROLLBACK EVERYTHING;
```

Você precisa de uma ação compensatória:

``` text
Release Stock
Cancel Order
```

Isso é a essência de Saga.

## Choreography

Serviços reagem a eventos.

## Orchestration

Um coordenador conduz o workflow.

## ⚖️ Escolha

Fluxo pequeno e naturalmente reativo?

Choreography pode ser suficiente.

Workflow longo, regras de compensação e muitos passos?

Orchestration pode deixá-lo mais explícito.

📚 https://microservices.io/patterns/data/saga.html

------------------------------------------------------------------------

# Aula 27 --- Transactional Outbox: um dos problemas mais importantes

Código aparentemente inocente:

``` csharp
await db.SaveChangesAsync();
await bus.Publish(new OrderCreated(...));
```

Existe uma janela:

``` text
DB ✓
CRASH
EVENTO ✗
```

Trocar a ordem:

``` text
EVENTO ✓
CRASH
DB ✗
```

também falha.

## 🛠️ Outbox

Mesma transação:

``` text
BEGIN
  INSERT Order
  INSERT OutboxMessage
COMMIT
```

Worker:

``` text
Outbox
  ↓
Message Broker
```

Agora a intenção de publicar sobrevive ao crash.

## 🤯 Mas espere...

Worker publica.

Antes de marcar como processado:

``` text
CRASH
```

Ele publicará novamente.

Outbox nos leva naturalmente a **idempotência**.

📚 https://microservices.io/patterns/data/transactional-outbox.html

------------------------------------------------------------------------

# Aula 28 --- Idempotência: "e se acontecer duas vezes?"

Pergunta que você deve criar o hábito de fazer:

> **E se essa operação acontecer duas vezes?**

Pagamento:

``` text
POST /charge
```

Cliente recebe timeout e tenta novamente.

O primeiro request talvez tenha cobrado.

Sem idempotência:

``` text
R$100
R$100
```

duas cobranças.

Com uma chave:

``` text
Idempotency-Key: checkout-123
```

o servidor reconhece a operação repetida e devolve o resultado
anterior/compatível sem repetir o efeito.

A mesma ideia vale para consumers.

📚
https://microservices.io/patterns/communication-style/idempotent-consumer.html

------------------------------------------------------------------------

# Aula 29 --- Timeout, Retry e Circuit Breaker são uma família

Dependency:

``` text
Orders → Payments
```

Payments fica lento.

## Sem timeout

Orders espera.

Recursos acumulam.

Orders também começa a falhar.

## Timeout

Define um limite.

## Retry

Tenta novamente quando a falha pode ser transitória.

## Problema

Se 20 mil requests fizerem retry imediatamente:

``` text
serviço doente
+
mais carga
=
serviço ainda mais doente
```

Use:

``` text
backoff
jitter
limites
```

## Circuit Breaker

Depois de falhas suficientes:

``` text
CLOSED → OPEN
```

Pare temporariamente de chamar.

Depois:

``` text
OPEN → HALF-OPEN
```

teste se recuperou.

## Pergunta crítica

> É seguro repetir essa operação?

Retry e idempotência são conceitos intimamente relacionados.

📚 https://microservices.io/patterns/reliability/circuit-breaker.html\
📚 https://learn.microsoft.com/en-us/azure/architecture/patterns/retry

------------------------------------------------------------------------

# Aula 30 --- Observabilidade: "o sistema está dizendo o que aconteceu?"

Logs:

``` text
o que aconteceu?
```

Metrics:

``` text
com que frequência/quanto?
```

Traces:

``` text
por onde esta requisição passou?
```

Exemplo:

``` text
React
  ↓ traceId=123
API
  ↓
Orders
  ↓
RabbitMQ
  ↓
Billing
  ↓
Payment Provider
```

Distributed tracing conecta a história.

## Perguntas que seu sistema deveria responder

``` text
Qual endpoint está lento?
Desde quando?
Qual dependência?
Qual versão introduziu?
Quantos usuários?
Qual request gerou esta mensagem?
Onde uma Saga parou?
```

Se você não consegue responder, o sistema está difícil de operar.

📚 https://opentelemetry.io/docs/

------------------------------------------------------------------------

# PARTE VII --- REACT E REACT NATIVE COMO ARQUITETURA

# Aula 31 --- Estado não é uma coisa só

Antes de instalar uma store, classifique:

``` text
UI STATE
modal aberto

FORM STATE
campos do formulário

URL STATE
filtros/paginação

SERVER STATE
orders/products/users

DERIVED STATE
total calculado a partir de outros dados
```

Problemas aparecem quando tudo vira:

``` text
global state
```

## Pergunta

> Quem é o verdadeiro dono deste estado?

📚 https://react.dev/learn/managing-state

------------------------------------------------------------------------

# Aula 32 --- Server State

Dados vindos da API possuem propriedades especiais:

``` text
ficam stale
podem mudar sem você
precisam de cache
podem falhar
precisam de refetch
```

Por isso ferramentas como TanStack Query tratam server state como
problema próprio.

Conceitos para dominar:

``` text
query keys
staleTime
invalidation
mutation
optimistic update
prefetch
```

Não memorize APIs.

Entenda:

> **Quando meus dados deixam de ser confiáveis e como sincronizo a UI
> novamente?**

📚 https://tanstack.com/query/latest/docs/framework/react/overview

------------------------------------------------------------------------

# Aula 33 --- Arquitetura por feature

Em aplicações grandes:

``` text
features/
  orders/
    api/
    components/
    hooks/
    pages/
  catalog/
```

pode facilitar ownership e mudança.

Mas evite:

``` text
shared/
```

virar um buraco negro com 400 arquivos.

Antes de mover algo para shared:

> Existem realmente dois consumidores independentes?

Duplicação pequena pode ser mais barata que uma abstração ruim.

------------------------------------------------------------------------

# Aula 34 --- React Native: rede ruim é arquitetura

No desktop com fibra:

``` text
request → response
```

No celular:

``` text
4G
↓
elevador
↓
sem sinal
↓
Wi-Fi
↓
app em background
↓
processo encerrado
```

Perguntas:

``` text
O que funciona offline?
O que precisa persistir?
Uma mutation pode ser repetida?
Como reconciliar?
Como informar estado pendente?
```

Offline-first é principalmente um problema de **consistência e UX**, não
apenas de armazenamento local.

------------------------------------------------------------------------

# PARTE VIII --- DESIGN SYSTEM

# Aula 35 --- Design System não é uma pasta `components`

Pense em camadas:

``` text
PRINCÍPIOS
   ↓
TOKENS
   ↓
PRIMITIVES
   ↓
COMPONENTS
   ↓
PATTERNS
   ↓
PRODUCT UI
```

Um Button é apenas uma pequena parte.

Design System inclui:

``` text
linguagem
regras
acessibilidade
documentação
governança
versionamento
contribuição
```

------------------------------------------------------------------------

# Aula 36 --- Tokens: dê significado às decisões visuais

Primitive:

``` text
blue-500 = #...
space-4 = 16px
```

Semantic:

``` text
color-action-primary
color-text-danger
spacing-component-gap
```

Por que semantic tokens?

Tema escuro.

Se seu componente usa:

``` text
blue-500
```

ele conhece uma implementação.

Se usa:

``` text
color-action-primary
```

ele conhece a **intenção**.

É quase a mesma ideia de Dependency Inversion aplicada ao design.

💡 Essa conexão é importante: boas abstrações escondem detalhes e expõem
intenção.

📚 https://www.designtokens.org/\
📚
https://www.w3.org/community/reports/design-tokens/CG-FINAL-format-20251028/

------------------------------------------------------------------------

# Aula 37 --- Web e Native não precisam compartilhar tudo

Objetivo ruim:

> "Quero 100% do código compartilhado."

Objetivo melhor:

> "Quero uma experiência e linguagem visual consistentes."

Compartilhe quando fizer sentido:

``` text
tokens
nomenclatura
princípios
contratos conceituais
```

Permita:

``` text
Button.web.tsx
Button.native.tsx
```

quando as plataformas possuem necessidades diferentes.

**Percentual de código compartilhado não é KPI de arquitetura.**

------------------------------------------------------------------------

# Aula 38 --- Component API é design de API

Compare:

``` tsx
<Button
  blue
  rounded
  bold
  height={42}
  loadingIcon
/>
```

com uma API orientada à intenção:

``` tsx
<Button
  variant="primary"
  size="medium"
  loading
/>
```

O consumidor deveria expressar **o que deseja**, não reconstruir as
regras visuais do Design System.

## Perguntas

``` text
Quais estados existem?
Quais combinações são inválidas?
Como evoluir sem breaking changes?
Como funciona teclado?
Como funciona screen reader?
```

📚 https://storybook.js.org/docs/writing-docs\
📚 https://www.w3.org/TR/WCAG22/

------------------------------------------------------------------------

# PARTE IX --- O LABORATÓRIO SÊNIOR

# Aula 39 --- Architecture Challenge 1: checkout

## Cenário

Seu e-commerce possui:

``` text
200 pedidos/min normalmente
2.000 pedidos/min em promoção
```

O pagamento externo:

``` text
P50 = 300 ms
P95 = 900 ms
às vezes fica indisponível por 20 s
```

Regra:

``` text
nenhum pedido pode ser cobrado duas vezes
```

## Pare aqui e pense antes de ler.

Perguntas:

``` text
Pagamento precisa ser síncrono?
Qual timeout?
Retry é permitido?
Como garantir idempotência?
Pedido nasce antes ou depois do pagamento?
Como lidar com callback/webhook?
Como reconciliar estado desconhecido?
Como medir falhas?
```

## Uma solução possível

Não existe resposta única.

Uma arquitetura poderia usar:

``` text
Checkout
   ↓
Create Order(Pending)
   ↓
Payment
   ↓
Approved → Confirm
Failed   → Reject
Unknown  → Reconcile
```

Com:

``` text
idempotency key
timeout
retry apenas quando seguro
webhook idempotente
job de reconciliação
tracing
métricas
```

A parte sênior é conseguir explicar **por que**.

------------------------------------------------------------------------

# Aula 40 --- Architecture Challenge 2: última unidade

## Cenário

``` text
Produto: PlayStation
Estoque: 1
Usuários simultâneos: 500
```

Pergunte:

``` text
Venda acima do estoque é aceitável?
Reserva expira?
Quanto tempo?
Pagamento ocorre antes/depois?
Precisamos de fairness?
Qual throughput?
```

Possibilidades:

``` text
optimistic concurrency
atomic UPDATE
reservation
queue
```

Não escolha "fila" só porque há concorrência.

Descubra a regra de negócio primeiro.

------------------------------------------------------------------------

# Aula 41 --- Architecture Challenge 3: notificações

Precisamos enviar:

``` text
Email
SMS
Push
```

para milhões de eventos.

Pergunte:

``` text
Entrega precisa ser imediata?
Pode duplicar?
Ordem importa?
Qual provider?
Rate limit?
Como retry funciona?
O que vai para DLQ?
Como usuário cancela preferência?
```

Arquitetura possível:

``` text
Business Event
      ↓
Notification Queue
      ↓
Notification Worker
 ┌────┼────┐
Email SMS Push
```

Agora quebre:

``` text
Provider de SMS fora do ar.
```

O Email deve parar?

Se sim, existe acoplamento de falha.

Pense em Bulkhead.

------------------------------------------------------------------------

# Aula 42 --- Architecture Challenge 4: ERP legado

Sistema:

``` text
15 anos
1 banco compartilhado
400 tabelas
deploy demora 2h
equipes bloqueiam umas às outras
```

Alguém propõe:

> "Vamos reescrever tudo em microservices."

Perguntas:

``` text
Quais módulos mais mudam?
Onde estão boundaries reais?
Qual dor queremos reduzir primeiro?
Podemos extrair incrementalmente?
Como migrar dados?
Como evitar big-bang rewrite?
```

Investigue Strangler Fig.

📚 https://martinfowler.com/bliki/StranglerFigApplication.html

------------------------------------------------------------------------

# PARTE X --- CRONOGRAMA ATÉ DEZEMBRO

# Setembro --- aprender a enxergar problemas

## Semana 1

**Tema:** decisão técnica

``` text
[ ] Fato vs hipótese
[ ] Trade-offs
[ ] ADR
[ ] resolver 2 decisões reais
```

## Semana 2

**Tema:** Strategy, Factory, Adapter

``` text
[ ] reconhecer problema
[ ] implementar exemplos pequenos
[ ] encontrar exemplo no trabalho
```

## Semana 3

**Tema:** Decorator, State, Chain

``` text
[ ] reconhecer problema
[ ] explicar quando NÃO usar
[ ] refatorar um exemplo
```

------------------------------------------------------------------------

# Outubro --- modelar e estruturar

## Semana 4

**Tema:** Bounded Context

``` text
[ ] criar Context Map de um domínio conhecido
[ ] identificar ownership
```

## Semana 5

**Tema:** Entity, VO, Aggregate

``` text
[ ] modelar Order
[ ] escrever invariantes
[ ] definir transaction boundary
```

## Semana 6

**Tema:** Clean/Hexagonal + Vertical Slice

``` text
[ ] desenhar dependências
[ ] identificar abstrações inúteis
```

## Semana 7

**Tema:** Modular Monolith vs Microservices

``` text
[ ] defender Modular Monolith
[ ] depois defender Microservices
[ ] escrever ADR final
```

------------------------------------------------------------------------

# Novembro --- sistemas que falham

## Semana 8

**Tema:** performance e banco

``` text
[ ] execution plan
[ ] índice
[ ] benchmark antes/depois
```

## Semana 9

**Tema:** concorrência + idempotência

``` text
[ ] simular race condition
[ ] implementar proteção
```

## Semana 10

**Tema:** Outbox

``` text
[ ] implementar
[ ] matar processo entre etapas
[ ] observar recuperação
```

## Semana 11

**Tema:** Saga

``` text
[ ] implementar fluxo
[ ] provocar PaymentRejected
[ ] compensar
```

## Semana 12

**Tema:** resiliência

``` text
[ ] timeout
[ ] retry
[ ] jitter
[ ] circuit breaker
[ ] medir comportamento
```

------------------------------------------------------------------------

# Dezembro --- agir como sênior

A partir daqui, reduza conteúdo novo.

## Semana 13

**Architecture Challenge:** Checkout

Produza:

``` text
requirements
NFRs
diagrama
failure modes
trade-offs
ADR
```

## Semana 14

**Architecture Challenge:** sistema de notificações

Mesmo processo.

## Semana 15

**Architecture Challenge:** modernização de legado

Mesmo processo.

## Semana 16

**Architecture Review do seu próprio projeto**

Pergunte:

``` text
Onde existe acoplamento?
Quais decisões são acidentais?
Quais abstrações não pagam seu custo?
Quais failure modes ignorei?
O que não consigo observar?
Onde há inconsistência?
O que eu simplificaria?
```

------------------------------------------------------------------------

# PARTE XI --- ROTINA SEMANAL

Não existe obrigação de estudar todos os dias.

Uma boa semana:

``` text
SEG — 35 min
Aula

TER — 35 min
Exercício

QUA — livre

QUI — 35 min
Cenário real

SEX — 20 min
ADR / resumo

SÁB — 90 min
Projeto / Architecture Challenge

DOM — nada
```

Total:

``` text
~3h35
```

Semana com energia:

adicione mais sessões.

Semana difícil:

faça apenas:

``` text
2 × 20 min
```

O plano não quebra.

------------------------------------------------------------------------

# PARTE XII --- O DIÁRIO DE ENGENHARIA

Crie:

``` text
engineering-journal/
```

Cada decisão interessante:

``` markdown
# 2026-XX-XX — Título

## Situação
...

## Minha primeira ideia
...

## Perguntas que fiz
...

## O que descobri
...

## Alternativas
...

## Decisão
...

## O que eu faria diferente
...
```

No fim de dezembro, releia.

Você deverá conseguir enxergar uma mudança:

``` text
ANTES
“qual pattern uso?”

DEPOIS
“qual problema existe?”
“qual evidência?”
“qual custo?”
“qual alternativa?”
```

------------------------------------------------------------------------

# PARTE XIII --- PERGUNTAS QUE VOCÊ DEVE AUTOMATIZAR NA CABEÇA

## Ao receber uma feature

``` text
Qual problema de negócio?
Qual comportamento esperado?
Quais edge cases?
Quais invariantes?
Quem é dono dos dados?
```

## Ao revisar código

``` text
Está correto?
Está compreensível?
Está testável?
Qual risco?
Existe acoplamento desnecessário?
A abstração paga seu custo?
```

## Ao integrar API externa

``` text
Timeout?
Retry?
Idempotência?
Rate limit?
Contrato muda?
Fallback?
Observabilidade?
PII/secrets?
```

## Ao criar evento

``` text
É Command ou Event?
Pode duplicar?
Ordem importa?
Quem é dono?
Schema pode evoluir?
Consumer é idempotente?
```

## Ao sugerir microservice

``` text
Qual boundary?
Qual problema resolve?
Deploy independente é necessário?
Escala independente?
Ownership independente?
Como dados serão separados?
Como observar?
Como testar?
Como operar?
```

## Ao otimizar

``` text
Qual métrica?
Qual baseline?
Qual SLO?
Onde está o gargalo?
Como provaremos melhoria?
```

## Ao escolher pattern

``` text
Qual força está causando complexidade?
Pattern reduz essa força?
Existe solução mais simples?
Qual indireção ele adiciona?
```

------------------------------------------------------------------------

# PARTE XIV --- REFERÊNCIAS ESSENCIAIS, COM MOTIVO

Estas referências não são "tarefas de leitura". A aula principal está
neste documento.

Use-as quando uma pergunta despertar curiosidade e você quiser ir um
nível abaixo.

## Refactoring.Guru

**Design Patterns**\
https://refactoring.guru/design-patterns

Use para visualizar intenção, estrutura e exemplos dos patterns GoF.

**Strategy**\
https://refactoring.guru/design-patterns/strategy

**Factory Method**\
https://refactoring.guru/design-patterns/factory-method

**Adapter**\
https://refactoring.guru/design-patterns/adapter

**Decorator**\
https://refactoring.guru/design-patterns/decorator

**State**\
https://refactoring.guru/design-patterns/state

**Chain of Responsibility**\
https://refactoring.guru/design-patterns/chain-of-responsibility

------------------------------------------------------------------------

## microservices.io --- Chris Richardson

**Pattern Language**\
https://microservices.io/patterns/

Use como mapa dos problemas que aparecem quando um sistema é
distribuído.

**Microservice Architecture**\
https://microservices.io/patterns/microservices.html

**Database per Service**\
https://microservices.io/patterns/data/database-per-service.html

**Saga**\
https://microservices.io/patterns/data/saga.html

**Transactional Outbox**\
https://microservices.io/patterns/data/transactional-outbox.html

**API Composition**\
https://microservices.io/patterns/data/api-composition.html

**Idempotent Consumer**\
https://microservices.io/patterns/communication-style/idempotent-consumer.html

**Circuit Breaker**\
https://microservices.io/patterns/reliability/circuit-breaker.html

------------------------------------------------------------------------

## Martin Fowler

**CQRS**\
https://martinfowler.com/bliki/CQRS.html

Importante para entender tanto o benefício quanto o custo de separar
modelos.

**Event-Driven Architecture**\
https://martinfowler.com/articles/201701-event-driven.html

Importante porque "event-driven" pode significar coisas diferentes.

**Strangler Fig**\
https://martinfowler.com/bliki/StranglerFigApplication.html

Importante para pensar em modernização incremental em vez de reescrita
big-bang.

------------------------------------------------------------------------

## Microsoft / .NET

**Common Web Application Architectures**\
https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures

**Cloud Design Patterns**\
https://learn.microsoft.com/en-us/azure/architecture/patterns/

**EF Core --- Efficient Querying**\
https://learn.microsoft.com/en-us/ef/core/performance/efficient-querying

**ADR**\
https://learn.microsoft.com/en-us/azure/well-architected/architect-role/architecture-decision-record

------------------------------------------------------------------------

## React

**Managing State**\
https://react.dev/learn/managing-state

**TanStack Query**\
https://tanstack.com/query/latest/docs/framework/react/overview

------------------------------------------------------------------------

## Design System

**Storybook**\
https://storybook.js.org/docs

**Design Tokens Community Group**\
https://www.designtokens.org/

**Design Tokens Format**\
https://www.w3.org/community/reports/design-tokens/CG-FINAL-format-20251028/

**WCAG 2.2**\
https://www.w3.org/TR/WCAG22/

------------------------------------------------------------------------

## Observabilidade

**OpenTelemetry**\
https://opentelemetry.io/docs/

------------------------------------------------------------------------

# PARTE XV --- CHECKPOINT FINAL

Em 31 de dezembro, pegue este cenário:

> "Precisamos construir checkout para 5.000 pedidos/minuto. O pagamento
> é externo, estoque é limitado, temos Web e Mobile e nenhum pedido pode
> ser cobrado duas vezes."

Sem pesquisar primeiro, conduza:

``` text
1. perguntas de negócio
2. requisitos funcionais
3. NFRs
4. invariantes
5. boundaries
6. modelo de dados
7. APIs
8. consistência
9. concorrência
10. idempotência
11. failure modes
12. resiliência
13. observabilidade
14. segurança
15. alternativas
16. trade-offs
17. ADR
18. métricas
```

Não existe necessidade de acertar uma arquitetura "perfeita".

O teste é:

> **Você consegue tornar seu raciocínio explícito?**

------------------------------------------------------------------------

# A ideia mais importante de todo o curso

Imagine dois desenvolvedores.

### Desenvolvedor A

Conhece:

``` text
CQRS
Saga
Outbox
Kafka
Redis
DDD
Clean Architecture
Strategy
Factory
```

Quando recebe um problema, começa escolhendo uma dessas ferramentas.

### Desenvolvedor B

Começa perguntando:

``` text
Qual problema?
Qual impacto?
Quais fatos?
Quais restrições?
O que precisa ser consistente?
O que acontece se falhar?
Qual solução mais simples?
Como vamos medir?
```

E às vezes conclui:

> "Não precisamos de nenhum pattern novo."

O desenvolvedor B está demonstrando o tipo de raciocínio que este plano
quer desenvolver.

Patterns, arquiteturas e tecnologias são **vocabulário e ferramentas**.

A competência central é:

# tomar boas decisões sob restrições reais.

------------------------------------------------------------------------

# Seu placar

Não marque "li".

Marque:

``` text
0 — nunca vi
1 — reconheço
2 — explico
3 — implemento
4 — comparo alternativas
5 — sei quando NÃO usar
6 — já defendi uma decisão real
```

  Tema                   Set   Out   Nov   Dez
  -------------------- ----- ----- ----- -----
  Decisão técnica                        
  Trade-offs                             
  Design Patterns                        
  DDD                                    
  Boundaries                             
  Modular Monolith                       
  CQRS                                   
  SQL/Performance                        
  Concorrência                           
  Idempotência                           
  Outbox                                 
  Saga                                   
  Resiliência                            
  Observabilidade                        
  React Architecture                     
  React Native                           
  Design System                          
  System Design                          
  ADR                                    

------------------------------------------------------------------------

> **Meta de dezembro:** não saber tudo.\
> **Meta de dezembro:** quando surgir algo que você não sabe, saber
> investigar, formular hipóteses, comparar alternativas, experimentar,
> decidir e explicar.
