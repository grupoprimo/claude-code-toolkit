# TDD - Integração com o Stripe

## Contexto

O FakeFlix vai lançar uma plataforma de streaming global e decidiu integrar o Stripe para
pagamentos online confiáveis e escaláveis.

Motivo do Projeto
A empresa precisa de um sistema robusto para processar assinaturas recorrentes e atender a
assinantes em vários países, substituindo as soluções internas que exigem alto esforço de
manutenção. O Stripe permite suporte multinacional, múltiplas moedas e métodos de
pagamento variados.

Depois de pesquisar entre vários gateways ele oferece a melhor taxa e experiência de
desenvolvimento.

## Glossário

```
Figma board
Referências links importantes
Link para o Epic link do jira
```

```
Tech Lead @Waldemar Neto
PM João
Time Ana, Pedro, Maria
```

```
Recursos
```

```
API (Application
Programming
Interface)
```

```
Conjunto de métodos e endpoints que permitem a comunicação entre
sistemas. A API do Stripe possibilita criar, gerenciar e acompanhar
transações e recursos de pagamento de forma segura.
```

```
Termo Descrição
```

API Key (Chave de
API)

```
Credenciais fornecidas pelo Stripe para autenticação de chamadas à
API. Há chaves de teste (test keys) e de produção (live keys),
fundamentais para manter segurança e separar ambientes de
desenvolvimento e produção.
```

Tokenização Substituição de dados confidenciais (ex.: dados de cartão) por tokens,
reduzindo o risco no armazenamento dessas informações.

Checkout Session Sessão de checkout criada para facilitar o processo de pagamento,
fornecendo uma interface pronta em que o cliente insere suas
informações. O Stripe gerencia todo o fluxo de forma segura.

PaymentIntent Objeto central no Stripe que representa o fluxo de pagamento. Ele
gerencia o processo de autorização, captura e processamento dos
fundos de forma previsível.

Subscription
(Assinatura)

```
Recurso para gerenciar pagamentos recorrentes, possibilitando
cobrança em intervalos definidos, alteração de planos e suspensão ou
cancelamento de assinaturas.
```

Charge Representa a tentativa de cobrança de um meio de pagamento.
Embora ainda seja utilizado, é recomendado o uso de PaymentIntents
para ter mais controle e compatibilidade com recursos de autenticação.

PaymentMethod Registra detalhes de pagamento, como cartões de crédito ou contas
bancárias. Pode ser associado a um Customer e utilizado em
transações futuras.

Customer (Cliente) Entidade que representa o usuário pagante. É onde são associados
métodos de pagamento, histórico de transações e detalhes de
faturamento.

Dashboard Interface web do Stripe onde se gerenciam transações, clientes,
faturamento, chaves de API, configurações e relatórios financeiros.

Webhooks Mecanismo de notificação assíncrona que envia eventos do Stripe para
o seu sistema, permitindo ações automatizadas, como gerenciamento
de reembolsos, confirmação de pagamentos e atualização de status de
pedidos.

SCA (Strong
Customer
Authentication)

```
Requisito de autenticação forte para transações, comum na Europa
(PSD2). O Stripe oferece recursos para lidar com 3D Secure e etapas
adicionais de verificação, reduzindo fraudes e atendendo a
regulamentações.
```

3D Secure Camada extra de autenticação para pagamentos com cartão. É
acionada durante a transação para confirmar que o comprador é o
titular legítimo do cartão.

Billing Funções do Stripe relacionadas à cobrança, incluindo criação de
faturas, gestão de assinaturas, cupons de desconto e planos de
preços.

Refund (Reembolso) Funcionalidade que permite devolver valores ao cliente, seja integral ou
parcial. Pode ser usada em casos de cancelamento de compra,
insatisfação ou erros de cobrança.

Invoice (Fatura) Documento de cobrança gerado, que mostra a quantia devida, itens
cobrados e impostos aplicáveis. Pode ser enviada automaticamente
por e-mail ao cliente.

Dispute
(Contestação)

```
Ocorre quando o cliente contesta um débito, gerando uma disputa
junto ao emissor do cartão. O Stripe facilita a análise e resposta às
contestações, bem como o reembolso, se necessário.
```

PCI Compliance Normas de segurança de dados para a indústria de cartões de
pagamento (PCI DSS). O Stripe cuida de grande parte desses
requisitos, reduzindo a responsabilidade do comerciante em lidar com
dados sensíveis.

## APIs Que Vamos Integrar

| API | Descrição | Principais Métodos/Endpoints |
|-----|-----------|------------------------------|
| `/v1/customers` | Cria e gerencia clientes no Stripe. É onde se associam os métodos de pagamento e dados de cobrança. | `POST /v1/customers`: cria um novo cliente. `GET /v1/customers/:id`: obtém informações de um cliente existente. |
| `/v1/payment_methods` | Registra e gerencia os métodos de pagamento (cartões, contas bancárias etc.) vinculados a um cliente. | `POST /v1/payment_methods`: cria um método de pagamento. `POST /v1/payment_methods/:id/attach`: vincula ao cliente. |
| `/v1/setup_intents` | Facilita a configuração de um método de pagamento para uso futuro, especialmente para assinaturas. | `POST /v1/setup_intents`: cria um SetupIntent. `GET /v1/setup_intents/:id`: obtém detalhes do SetupIntent. |
| `/v1/subscriptions` | Cria, atualiza e cancela assinaturas, vinculando planos, clientes e métodos de pagamento em um fluxo recorrente. | `POST /v1/subscriptions`: cria uma nova assinatura. `DELETE /v1/subscriptions/:id`: cancela assinatura. `GET /v1/subscriptions/:id`: obtém detalhes. |
| `/v1/prices` | Define e gerencia os planos de cobrança, especificando valores e intervalos (mensal, anual etc.). | `POST /v1/prices`: cria um preço/Plano. `GET /v1/prices`: lista preços existentes. |
| `/v1/invoices` | Gera faturas para as assinaturas, detalhando valores, descontos, impostos etc. | `GET /v1/invoices`: lista faturas. `GET /v1/invoices/:id`: obtém detalhes de uma fatura específica. |
| `/v1/webhook_endpoints` | Gerencia os endpoints para receber eventos (ex.: assinatura criada, cancelada, pagamento falhou etc.) | `POST /v1/webhook_endpoints`: cria um endpoint de webhook. `DELETE /v1/webhook_endpoints/:id`: exclui um webhook. |

**Observações:**
- Em muitos cenários, utiliza-se também o PaymentIntent (`/v1/payment_intents`) para autorizar e capturar pagamentos únicos; porém, para assinaturas, a maior parte do fluxo gira em torno de `/v1/subscriptions`, `/v1/prices` e `/v1/invoices`.
- Os métodos de `setup_intents` podem ser necessários quando se quer garantir que um cartão ou conta esteja válido antes de iniciar a cobrança recorrente.

## Fluxo de Trial Gratuito - Visão Técnica - MVP

O modo trial representa o cenário mais simplificado de onboarding de usuários, permitindo
acesso imediato ao serviço sem necessidade de informações de pagamento. Este fluxo é
estratégico para reduzir fricção na conversão e aumentar a taxa de ativação de usuários.

### Fluxo Técnico:

1. **Frontend → Backend API**: `POST /subscription/v2` com apenas `priceId` e `trialDays` (sem `paymentMethodId`)
2. **Backend busca usuário autenticado**: obtém dados completos do Identity Module via GraphQL
3. **Backend → Stripe API**: Busca Customer existente por email ou cria novo Customer automaticamente
4. **Backend → Stripe API**: Cria Subscription em modo trial (`trial_period_days: 14`) sem método de pagamento
5. **Stripe ativa subscription** imediatamente com status `trialing`
6. **Backend armazena cache local** nas entidades `GatewayCustomer` e `GatewaySubscription` para performance

### APIs Envolvidas:

- **Stripe Customers API** (`/customers`) - Gestão de clientes
- **Stripe Subscriptions API** (`/subscriptions`) - Criação de assinaturas com trial
- **Identity GraphQL API** (`/graphql`) - Obtenção de dados do usuário autenticado
- **Database Local** - Cache de customers e subscriptions para consultas rápidas

### Webhook de Gestão:

Durante o trial, o sistema receberá webhooks do Stripe para eventos como `customer.subscription.trial_will_end` (3 dias antes do fim) e `customer.subscription.updated` (quando trial expira), permitindo comunicação proativa com o usuário para conversão.

## Solução

| Tarefa | Descrição | Status |
|--------|-----------|--------|
| **Setup** | | |
| Configurar Credenciais do Stripe | Obter as chaves de API (test e live) no painel do Stripe e armazená-las de forma segura (por exemplo, em variáveis de ambiente). | DEFINIDO |
| Definir Ambiente de Teste (Sandbox) | Preparar a aplicação para utilizar as chaves de teste e endpoints apropriados do Stripe. Vamos usar o ambiente de staging como o teste do Stripe. | DEFINIDO |
| **Integração - Cliente** | | |
| Criação de Clientes (Customers) | Implementar a comunicação com a API do Stripe para criar e gerenciar registros de clientes. | DEFINIDO |
| Busca de Customers | Implementar API que busca customer por email do Stripe. | DEFINIDO |
| Gerenciamento de Métodos de Pagamento | Utilizar a API de Payment Methods ou SetupIntents para registrar e validar cartões de crédito, carteiras digitais ou contas bancárias. | FORA DE ESCOPO |
| **Gerenciamento de assinaturas** | | |
| Criação de Assinaturas (Subscriptions) | Desenvolver o fluxo de criação de planos/preços (Prices) e assinatura (Subscription). | DEFINIDO |
| Cancelamento de Assinaturas | Implementar endpoints e lógica de negócio para cancelar assinaturas. | DEFINIDO |
| Integração com Webhooks | Configurar e gerenciar webhooks para receber notificações de eventos do Stripe. | INDEFINIDO |
| Tratamento de Erros e Falhas de Pagamento | Manusear exceções vindas das APIs do Stripe. | INDEFINIDO |
| **Extras** | | |
| Geração de Faturas e Relatórios | Implementar a listagem de invoices (faturas) e relatórios no sistema. | INDEFINIDO |

## Riscos

| Risco | Descrição | Possíveis Ações de Mitigação |
|-------|-----------|------------------------------|
| Falhas de segurança ou vazamento de dados sensíveis | A exposição de chaves de API ou informações de cartão de crédito pode gerar grandes prejuízos e comprometer a reputação da empresa. | Armazenar as credenciais em um local seguro (ex.: variáveis de ambiente). Revisar periodicamente a conformidade com padrões de segurança (PCI DSS). |
| Inconsistência no gerenciamento de assinaturas | Falhas na lógica de criação, renovação ou cancelamento podem levar a cobranças incorretas e insatisfação do usuário. | Automatizar testes em cenários de criação, renovação e cancelamento. Utilizar webhooks para manter o status atualizado em tempo real. |
| Falhas no recebimento e tratamento de Webhooks | Perder eventos importantes (pagamento falhou, disputa aberta etc.) pode gerar inconsistências no status de pagamento. | Garantir alta disponibilidade do endpoint de webhook. Implementar fila de processamento e reenvio automático de eventos não processados. |
| Dependência de Terceiros (Stripe) | Se o Stripe enfrentar instabilidade ou downtime, o sistema de assinaturas pode ficar indisponível. | Monitorar status do Stripe. Ter plano de contingência para lidar com falhas de serviço (por exemplo, fila de transações pendentes). |
| Adequação às legislações internacionais (LGPD, GDPR, PSD2) | Atender a diferentes regulações de privacidade e proteção de dados é complexo e requer atualizações frequentes. | Acompanhar mudanças regulatórias. Implementar recursos de conformidade (SCA, DPO, gestão de consentimento). |
