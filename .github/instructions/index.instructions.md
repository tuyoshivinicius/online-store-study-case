---
applyTo: '**'
---
# Bazarium - Arquitetura de E-commerce

## Sobre o Projeto

**Nome:** Projeto de Arquitetura para a Loja Bazarium
**Descrição:** Arquitetura de software baseada em microserviços para uma loja virtual, implementando padrões modernos de Event-Driven Architecture e Domain-Driven Design.

### Contexto de Negócio
- **Domínio:** E-commerce de produtos físicos
- **Modelo de Negócio:** B2C (Business to Consumer)
- **Fulfillment:** Produtos armazenados e entregues via empresa de logística terceirizada
- **Pagamentos:** Processamento via gateway de pagamento externo
- **Entrega:** Direta ao cliente final

## Arquitetura e Padrões

### Padrões de Integração
- **Choreography Saga Pattern** - Coordenação de transações distribuídas
- **Anti-Corruption Layer** - Proteção contra sistemas externos
- **Event Sourcing** - Auditoria e rastreabilidade
- **CQRS** - Separação de comandos e consultas

## Convenções de Desenvolvimento

### Nomenclatura
- **Entidades e Agregados:** PascalCase em inglês (ex: `OrderItem`, `PaymentDetails`)
- **Comandos:** PascalCase com verbo + substantivo (ex: `CreateOrder`, `ConfirmPayment`)
- **Eventos:** PascalCase no passado (ex: `OrderCreated`, `PaymentConfirmed`)
- **Policies:** PascalCase descritivo (ex: `TriggerReserveInventory`)
- **Services:** PascalCase + "Service" (ex: `OrderService`)
- **Repositories:** PascalCase + "Repository" (ex: `OrderRepository`)

### Documentação
- **Formato Base:** Markdown
- **Diagramas:** Mermaid (flowcharts, sequence, class diagrams)
- **Idioma Documentação:** Português
- **Idioma Código:** Inglês
- **Linguagem Código:** Python
- **Icones:** Não utilizar ícones, apenas texto
- **Comentários:** Português para explicações de negócio e código.

### Estrutura de Eventos
```markdown
### EventName
- **Descrição:** [Descrição em português]
- **Agregado de Origem:** [NomeDoAgregado]
- **Consequências:** [Lista de ações subsequentes]
- **Importância para o Negócio:** [Impacto no negócio]
```

### Estrutura de Comandos
```markdown
### CommandName
- **Descrição:** [Descrição em português]
- **Agregado Alvo:** [NomeDoAgregado]
- **Atores Possíveis:** [Customer, Sistema (Policy), etc.]
- **Eventos Resultantes:** [Lista de eventos]
- **Validações/Regras:** [Regras de negócio]
```

## Metodologias e Frameworks

### Design e Arquitetura
- **Domain-Driven Design (DDD)** - Modelagem orientada ao domínio
- **Event Storming** - Descoberta e modelagem colaborativa
- **Microservice Architecture** - Decomposição em serviços independentes
- **Event-Driven Architecture (EDA)** - Comunicação baseada em eventos
- **Choreography Saga Pattern** - Coordenação de transações distribuídas

### Qualidade e Governança
- **Testes de Domínio** - Validação de regras de negócio
- **Consumer-Driven Contracts** - Contratos entre serviços
- **API-First Design** - Definição de APIs antes da implementação
- **Event Schema Registry** - Versionamento de eventos