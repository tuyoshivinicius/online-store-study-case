# Event Storming


## Diagrama

```mermaid
---
config:
  theme: redux
  layout: dagre
---
flowchart TD
    n1["CreateOrder"] --> n2["Order"]
    n2 --> n3(["OrderCreated"])
    n4["CreatePayment"] --> n5["Payment"]
    n5 --> n6(["SuccessPaymentConfirmed"]) & n21(["PaymentCreated"]) & n48(["FailurePaymentConfirmed"])
    n7["CreateShipping"] --> n8["Shipping"]
    n8 --> n18(["ShippingDelivered"]) & n26(["ShippingCreated"]) & n52(["ShippingStarted"])
    n10["DeductInventory"] --> n11["Inventory"]
    n11 --> n12(["InventoryDeducted"]) & n31(["InventoryReserved"]) & n33(["InventoryReservationExpired"])
    n16(("Customer")) --> n1 & n4
    n18 --> n45["ListenShippingStatus"]
    n19["UpdateOrderStatus"] --> n2
    n20["ReserveInventory"] --> n11
    n21 --> n22["ProcessPayment"] & n53["ListenPaymentStatus"]
    n22 --> n23["PaymentGateway"]
    n23 --> n24(["PaymentProcessed"])
    n25["ConfirmSuccessPayment"] --> n5
    n26 --> n27["StartDelivery"] & n45
    n27 --> n28["DeliveryCompany"]
    n28 --> n29(["DeliveryCompleted"]) & n50(["DeliveryStarted"])
    n30["ConfirmShippingDelivery"] --> n8
    n31 --> n32["CheckReservationTimeout"]
    n32 --> n41["ExpireInventoryReservation"]
    n33 --> n46["CancelOrderInventoryReservationExpired"]
    n34["CancelOrder"] --> n2
    n6 --> n37["TriggerInvoiceGeneration"] & n53
    n12 --> n36["TriggerShippingCreation"]
    n35["TriggerInventoryDeduction"] --> n10
    n36 --> n7
    n37 --> n38["CreateInvoice"]
    n38 --> n39["Invoice"]
    n39 --> n40(["InvoiceCreated"])
    n40 --> n35
    n41 --> n11
    n24 --> n42["CheckPaymentSuccess"]
    n42 --> n25 & n47["ConfirmFailurePayment"]
    n3 --> n43["TriggerReserveInventory"]
    n43 --> n20
    n29 --> n44["ListenDeliveryStatus"]
    n44 --> n30 & n51["ConfirmShippingStart"]
    n45 --> n19
    n46 --> n34
    n47 --> n5
    n48 --> n49["TriggerCancelOrderPaymentFailure"] & n53
    n49 --> n34
    n51 --> n8
    n50 --> n44
    n52 --> n45
    n53 --> n19
    n1@{ shape: rect}
    n2@{ shape: hex}
    n4@{ shape: rect}
    n5@{ shape: hex}
    n8@{ shape: hex}
    n11@{ shape: hex}
    n45@{ shape: card}
    n53@{ shape: card}
    n23@{ shape: dbl-circ}
    n28@{ shape: dbl-circ}
    n32@{ shape: card}
    n46@{ shape: card}
    n34@{ shape: rect}
    n37@{ shape: card}
    n36@{ shape: card}
    n35@{ shape: card}
    n39@{ shape: hex}
    n42@{ shape: card}
    n43@{ shape: card}
    n44@{ shape: card}
    n49@{ shape: card}
     n1:::Sky
     n1:::Command
     n2:::Aggregate
     n3:::DomainEvent
     n4:::Command
     n5:::Aggregate
     n6:::DomainEvent
     n21:::DomainEvent
     n48:::DomainEvent
     n7:::Command
     n8:::Aggregate
     n18:::DomainEvent
     n26:::DomainEvent
     n52:::DomainEvent
     n10:::Command
     n11:::Aggregate
     n12:::DomainEvent
     n31:::DomainEvent
     n33:::DomainEvent
     n16:::Rose
     n16:::Actor
     n45:::Policy
     n19:::Command
     n20:::Command
     n22:::Command
     n53:::Policy
     n23:::ExternalSystem
     n24:::DomainEvent
     n25:::Command
     n27:::Command
     n28:::ExternalSystem
     n29:::DomainEvent
     n50:::DomainEvent
     n30:::Command
     n32:::Policy
     n41:::Command
     n46:::Policy
     n34:::Command
     n37:::Policy
     n36:::Policy
     n35:::Policy
     n38:::Command
     n39:::Aggregate
     n40:::DomainEvent
     n42:::Policy
     n47:::Command
     n43:::Policy
     n44:::Policy
     n51:::Command
     n49:::Policy
    classDef Actor stroke:#000000, fill:#FFCDD2, color:#000000
    classDef ExternalSystem stroke:#000000, fill:#00C853, color:#000000
    classDef Aggregate stroke-width:1px, stroke-dasharray: 0, stroke:#000000, fill:#FFD600, color:#000000
    classDef Command stroke:#000000, fill:#BBDEFB, color:#000000
    classDef DomainEvent stroke:#000000, fill:#FF6D00, color:#000000
    classDef Policy fill:#E1BEE7, stroke:#000000, color:#000000
```


## Actors


### Customer
- **Tipo:** Humano
- **Descrição:** Usuário da loja que realiza pedidos e pagamentos.
- **Principais Interações:** CreateOrder, CreatePayment

### PaymentGateway
- **Tipo:** Sistema Externo
- **Descrição:** Sistema de terceiros responsável pelo processamento de pagamentos.
- **Principais Interações:** Recebe ProcessPayment, retorna PaymentProcessed

### DeliveryCompany
- **Tipo:** Sistema Externo
- **Descrição:** Empresa responsável pela entrega dos pedidos.
- **Principais Interações:** Recebe StartDelivery, retorna DeliveryCompleted

## Aggregates


### Order
- **Descrição:** Representa o pedido realizado pelo cliente.
- **Entidades e Objetos de Valor:** OrderItem, CustomerInfo
- **Comandos Manipulados:** CreateOrder, UpdateOrderStatus, CancelOrder
- **Eventos Gerados:** OrderCreated
- **Regras de Negócio:** Validação de itens, status do pedido

### Payment
- **Descrição:** Gerencia o pagamento de um pedido.
- **Entidades e Objetos de Valor:** PaymentDetails
- **Comandos Manipulados:** CreatePayment, ConfirmSuccessPayment, ConfirmFailurePayment
- **Eventos Gerados:** PaymentCreated, SuccessPaymentConfirmed, FailurePaymentConfirmed
- **Regras de Negócio:** Validação de status de pagamento

### Shipping
- **Descrição:** Gerencia o processo de entrega do pedido.
- **Entidades e Objetos de Valor:** ShippingAddress
- **Comandos Manipulados:** CreateShipping, ConfirmShippingDelivery, StartDelivery, ConfirmShippingStart
- **Eventos Gerados:** ShippingCreated, ShippingDelivered, ShippingStarted, DeliveryCompleted, DeliveryStarted
- **Regras de Negócio:** Controle de status de entrega

### Inventory
- **Descrição:** Gerencia o estoque dos produtos.
- **Entidades e Objetos de Valor:** InventoryItem
- **Comandos Manipulados:** DeductInventory, ReserveInventory, ExpireInventoryReservation
- **Eventos Gerados:** InventoryDeducted, InventoryReserved, InventoryReservationExpired
- **Regras de Negócio:** Reserva e dedução de estoque

### Invoice
- **Descrição:** Representa a fatura gerada após o pagamento.
- **Entidades e Objetos de Valor:** InvoiceDetails
- **Comandos Manipulados:** CreateInvoice
- **Eventos Gerados:** InvoiceCreated
- **Regras de Negócio:** Geração de fatura após pagamento confirmado

## Commands


### CreateOrder
- **Descrição:** Cria um novo pedido para o cliente.
- **Agregado Alvo:** Order
- **Atores Possíveis:** Customer
- **Eventos Resultantes:** OrderCreated
- **Validações/Regras:** Itens válidos, estoque disponível

### CreatePayment
- **Descrição:** Inicia o processo de pagamento de um pedido.
- **Agregado Alvo:** Payment
- **Atores Possíveis:** Customer
- **Eventos Resultantes:** PaymentCreated
- **Validações/Regras:** Pedido existente, valor correto

### CreateShipping
- **Descrição:** Inicia o processo de entrega do pedido.
- **Agregado Alvo:** Shipping
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** ShippingCreated
- **Validações/Regras:** Pedido pago

### DeductInventory
- **Descrição:** Deduz o estoque dos itens do pedido.
- **Agregado Alvo:** Inventory
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** InventoryDeducted
- **Validações/Regras:** Estoque suficiente

### UpdateOrderStatus
- **Descrição:** Atualiza o status do pedido.
- **Agregado Alvo:** Order
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** -
- **Validações/Regras:** Status permitido

### ReserveInventory
- **Descrição:** Reserva o estoque para um pedido.
- **Agregado Alvo:** Inventory
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** InventoryReserved
- **Validações/Regras:** Estoque disponível

### ProcessPayment
- **Descrição:** Processa o pagamento via gateway externo.
- **Agregado Alvo:** Payment
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** PaymentProcessed
- **Validações/Regras:** Dados válidos

### ConfirmSuccessPayment
- **Descrição:** Confirma o pagamento bem-sucedido.
- **Agregado Alvo:** Payment
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** SuccessPaymentConfirmed
- **Validações/Regras:** Pagamento processado

### StartDelivery
- **Descrição:** Inicia a entrega do pedido.
- **Agregado Alvo:** Shipping
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** DeliveryStarted
- **Validações/Regras:** Pedido pronto para envio

### ConfirmShippingDelivery
- **Descrição:** Confirma a entrega do pedido ao cliente.
- **Agregado Alvo:** Shipping
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** ShippingDelivered
- **Validações/Regras:** Entrega realizada

### ExpireInventoryReservation
- **Descrição:** Expira a reserva de estoque não utilizada.
- **Agregado Alvo:** Inventory
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** InventoryReservationExpired
- **Validações/Regras:** Tempo de reserva expirado

### CancelOrder
- **Descrição:** Cancela o pedido do cliente.
- **Agregado Alvo:** Order
- **Atores Possíveis:** Customer, Sistema (Policy)
- **Eventos Resultantes:** -
- **Validações/Regras:** Pedido pode ser cancelado

### CreateInvoice
- **Descrição:** Gera a fatura do pedido.
- **Agregado Alvo:** Invoice
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** InvoiceCreated
- **Validações/Regras:** Pagamento confirmado

### ConfirmFailurePayment
- **Descrição:** Confirma o insucesso do pagamento.
- **Agregado Alvo:** Payment
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** FailurePaymentConfirmed
- **Validações/Regras:** Pagamento processado

### ConfirmShippingStart
- **Descrição:** Confirma o início da entrega.
- **Agregado Alvo:** Shipping
- **Atores Possíveis:** Sistema (Policy)
- **Eventos Resultantes:** ShippingStarted
- **Validações/Regras:** Entrega iniciada

## Domain Events


### OrderCreated
- **Descrição:** Um novo pedido foi criado.
- **Agregado de Origem:** Order
- **Consequências:** Reserva de estoque, início do pagamento
- **Importância para o Negócio:** Inicia o fluxo de compra

### SuccessPaymentConfirmed
- **Descrição:** O pagamento foi confirmado com sucesso.
- **Agregado de Origem:** Payment
- **Consequências:** Geração de fatura, início do envio
- **Importância para o Negócio:** Permite liberar o pedido

### PaymentCreated
- **Descrição:** O pagamento foi iniciado.
- **Agregado de Origem:** Payment
- **Consequências:** Processamento do pagamento
- **Importância para o Negócio:** Inicia a transação financeira

### FailurePaymentConfirmed
- **Descrição:** O pagamento falhou.
- **Agregado de Origem:** Payment
- **Consequências:** Cancelamento do pedido
- **Importância para o Negócio:** Garante consistência financeira

### ShippingDelivered
- **Descrição:** O pedido foi entregue ao cliente.
- **Agregado de Origem:** Shipping
- **Consequências:** Finaliza o fluxo de entrega
- **Importância para o Negócio:** Satisfação do cliente

### ShippingCreated
- **Descrição:** O envio foi criado.
- **Agregado de Origem:** Shipping
- **Consequências:** Início da entrega
- **Importância para o Negócio:** Logística

### ShippingStarted
- **Descrição:** A entrega foi iniciada.
- **Agregado de Origem:** Shipping
- **Consequências:** Monitoramento da entrega
- **Importância para o Negócio:** Controle logístico

### InventoryDeducted
- **Descrição:** O estoque foi deduzido.
- **Agregado de Origem:** Inventory
- **Consequências:** Atualização de estoque
- **Importância para o Negócio:** Controle de inventário

### InventoryReserved
- **Descrição:** O estoque foi reservado para o pedido.
- **Agregado de Origem:** Inventory
- **Consequências:** Garante disponibilidade
- **Importância para o Negócio:** Evita overbooking

### InventoryReservationExpired
- **Descrição:** A reserva de estoque expirou.
- **Agregado de Origem:** Inventory
- **Consequências:** Liberação de estoque
- **Importância para o Negócio:** Otimização de inventário

### PaymentProcessed
- **Descrição:** O pagamento foi processado pelo gateway.
- **Agregado de Origem:** Payment
- **Consequências:** Confirmação de sucesso ou falha
- **Importância para o Negócio:** Integração financeira

### DeliveryCompleted
- **Descrição:** A entrega foi concluída.
- **Agregado de Origem:** Shipping
- **Consequências:** Finalização do pedido
- **Importância para o Negócio:** Satisfação do cliente

### DeliveryStarted
- **Descrição:** A entrega foi iniciada pela transportadora.
- **Agregado de Origem:** Shipping
- **Consequências:** Rastreamento
- **Importância para o Negócio:** Logística

### InvoiceCreated
- **Descrição:** A fatura foi gerada.
- **Agregado de Origem:** Invoice
- **Consequências:** Envio ao cliente
- **Importância para o Negócio:** Obrigações fiscais

## Policies


### ListenShippingStatus
- **Descrição:** Monitora o status da entrega para atualizar o pedido.
- **Evento(s) de Disparo:** ShippingDelivered, DeliveryCompleted
- **Ações Executadas:** UpdateOrderStatus
- **Observações:** Coreografia

### ListenPaymentStatus
- **Descrição:** Monitora o status do pagamento para atualizar o pedido.
- **Evento(s) de Disparo:** SuccessPaymentConfirmed, FailurePaymentConfirmed
- **Ações Executadas:** UpdateOrderStatus, CancelOrder
- **Observações:** Coreografia

### TriggerInvoiceGeneration
- **Descrição:** Gera a fatura após pagamento confirmado.
- **Evento(s) de Disparo:** SuccessPaymentConfirmed
- **Ações Executadas:** CreateInvoice
- **Observações:** Coreografia

### TriggerShippingCreation
- **Descrição:** Cria o envio após estoque deduzido.
- **Evento(s) de Disparo:** InventoryDeducted
- **Ações Executadas:** CreateShipping
- **Observações:** Coreografia

### TriggerInventoryDeduction
- **Descrição:** Deduz o estoque após a criação da fatura.
- **Evento(s) de Disparo:** InvoiceCreated
- **Ações Executadas:** DeductInventory
- **Observações:** Coreografia

### CheckReservationTimeout
- **Descrição:** Verifica se a reserva de estoque expirou.
- **Evento(s) de Disparo:** InventoryReserved
- **Ações Executadas:** ExpireInventoryReservation
- **Observações:** Coreografia

### CancelOrderInventoryReservationExpired
- **Descrição:** Cancela o pedido se a reserva de estoque expirar.
- **Evento(s) de Disparo:** InventoryReservationExpired
- **Ações Executadas:** CancelOrder
- **Observações:** Coreografia

### TriggerReserveInventory
- **Descrição:** Reserva o estoque após a criação do pedido.
- **Evento(s) de Disparo:** OrderCreated
- **Ações Executadas:** ReserveInventory
- **Observações:** Coreografia

### ListenDeliveryStatus
- **Descrição:** Monitora o status da entrega para confirmar a entrega ao cliente.
- **Evento(s) de Disparo:** DeliveryCompleted, DeliveryStarted
- **Ações Executadas:** ConfirmShippingDelivery, ConfirmShippingStart
- **Observações:** Coreografia

### TriggerCancelOrderPaymentFailure
- **Descrição:** Cancela o pedido após falha no pagamento.
- **Evento(s) de Disparo:** FailurePaymentConfirmed
- **Ações Executadas:** CancelOrder
- **Observações:** Coreografia

### CheckPaymentSuccess
- **Descrição:** Verifica o sucesso do pagamento após processamento.
- **Evento(s) de Disparo:** PaymentProcessed
- **Ações Executadas:** ConfirmSuccessPayment, ConfirmFailurePayment
- **Observações:** Coreografia

