## 1. Ideia geral: onde entra callback/listener na Clean Architecture?

Em Clean Architecture você tem, grosso modo:

- **Domain** → Entidades, Value Objects, regras de negócio.
- **Application** → Use cases (orquestram o fluxo de negócio).
- **Interface/Entrypoints** → Controllers, CLI, mensageria, etc.
- **Infrastructure/Dataproviders** → Banco, mensageria, email, etc.

**Eventos de domínio** entram assim:

1. Algo importante acontece no domínio → ex: `Order` foi criada.
2. O use case **publica um evento de domínio** (OrderCreatedEvent).
3. Esse evento é encaminhado para `listeners` (callbacks), normalmente na camada de **aplicação ou infraestrutura**.
4. Cada listener reage ao evento: manda e-mail, grava log, publica em fila, etc.

Os **listeners** são justamente os **callbacks**:

> “Quando um evento X acontecer, execute esse código aqui.”

---

## 2. Modelando o contrato de evento no domínio

### 2.1. Interface base para eventos de domínio

```java
// domain/event/DomainEvent.java
package domain.event;

import java.time.Instant;

public interface DomainEvent {
    Instant occurredAt();
}

```

### 2.2. Exemplo de evento de domínio

```java
// domain/event/OrderCreatedEvent.java
package domain.event;

import domain.model.Order;
import java.time.Instant;

public class OrderCreatedEvent implements DomainEvent {

    private final Order order;
    private final Instant occurredAt;

    public OrderCreatedEvent(Order order) {
        this.order = order;
        this.occurredAt = Instant.now();
    }

    public Order getOrder() {
        return order;
    }

    @Override
    public Instant occurredAt() {
        return occurredAt;
    }
}

```

---

## 3. Port de publicação de eventos (a “saída” do domínio)

O domínio não deve conhecer **Spring**, **Kafka**, **Rabbit**, nada disso.

Então criamos uma **abstração**:

```java
// domain/event/DomainEventPublisher.java
package domain.event;

public interface DomainEventPublisher {
    void publish(DomainEvent event);
}

```

Essa interface é um **port de saída** (output port).

Quem implementa isso? A camada de **infraestrutura**.

---

## 4. Use Case disparando evento de domínio

No **Application Layer**, o use case faz:

- regra de orquestração (cria pedido, persiste, etc)
- chama `DomainEventPublisher.publish(...)`

```java
// application/usecase/CreateOrderUseCase.java
package application.usecase;

import domain.model.Order;
import domain.repository.OrderRepository;
import domain.event.DomainEventPublisher;
import domain.event.OrderCreatedEvent;

public class CreateOrderUseCase {

    private final OrderRepository orderRepository;
    private final DomainEventPublisher eventPublisher;

    public CreateOrderUseCase(OrderRepository orderRepository,
                              DomainEventPublisher eventPublisher) {
        this.orderRepository = orderRepository;
        this.eventPublisher = eventPublisher;
    }

    public Order execute(CreateOrderCommand command) {
        // 1. Monta entidade de domínio
        Order order = new Order(command.getCustomerId(), command.getItems());

        // 2. Regras de domínio internas (validações etc.)
        order.validate();

        // 3. Persiste
        orderRepository.save(order);

        // 4. Publica evento de domínio
        eventPublisher.publish(new OrderCreatedEvent(order));

        return order;
    }
}

```

Note que o use case só conhece **a interface** `DomainEventPublisher`.

Quem vai de fato “notificar os listeners” é a infraestrutura.

---

## 5. Infraestrutura: implementando o Publisher usando Spring (callback/listener)

Aqui entra o **callback/listener** de forma concreta.

### 5.1. Implementação do `DomainEventPublisher` com `ApplicationEventPublisher`

```java
// infrastructure/event/SpringDomainEventPublisher.java
package infrastructure.event;

import domain.event.DomainEvent;
import domain.event.DomainEventPublisher;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;

@Component
public class SpringDomainEventPublisher implements DomainEventPublisher {

    private final ApplicationEventPublisher applicationEventPublisher;

    public SpringDomainEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
        this.applicationEventPublisher = applicationEventPublisher;
    }

    @Override
    public void publish(DomainEvent event) {
        applicationEventPublisher.publishEvent(event);
    }
}

```

Aqui a gente está:

- adaptando o **port de domínio** (`DomainEventPublisher`)
- para o mecanismo específico do framework (Spring `ApplicationEventPublisher`).

---

## 6. Listener como Callback (a mágica)

Agora entram os **listeners**, que são exatamente os **callbacks**:

> “Quando um OrderCreatedEvent for publicado, execute esse método aqui.”

```java
// infrastructure/listener/SendEmailOnOrderCreated.java
package infrastructure.listener;

import domain.event.OrderCreatedEvent;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class SendEmailOnOrderCreated {

    private final EmailSender emailSender;

    public SendEmailOnOrderCreated(EmailSender emailSender) {
        this.emailSender = emailSender;
    }

    @EventListener
    public void handle(OrderCreatedEvent event) {
        var order = event.getOrder();
        var customerEmail = order.getCustomer().getEmail();

        emailSender.send(
            customerEmail,
            "Seu pedido foi criado!",
            "Detalhes do pedido: " + order.getId()
        );
    }
}

```

Esse método `handle` é um **callback**.

Ele **não é chamado diretamente** por você.

Quem chama é o **framework de eventos do Spring**, quando ele detecta que:

1. Um `OrderCreatedEvent` foi publicado;
2. Existe um método anotado com `@EventListener(OrderCreatedEvent.class)`.

Você delegou o controle para o framework → **Inversão de Controle + callback**.

---

## 7. Fluxo completo (ligando os pontos)

1. Controller HTTP (Entrypoint) recebe a requisição `POST /orders`.
2. Controller chama `CreateOrderUseCase.execute(...)`.
3. Use case:
    - cria `Order`
    - persiste via `OrderRepository`
    - publica `OrderCreatedEvent` via `DomainEventPublisher`
4. `SpringDomainEventPublisher.publish()` chama `ApplicationEventPublisher.publishEvent(event)`.
5. O Spring encontra todos os métodos anotados com `@EventListener` que aceitam `OrderCreatedEvent`.
6. Ele executa (chama) esses métodos → **callbacks**.
7. `SendEmailOnOrderCreated.handle(...)` envia o e-mail.

Nada disso quebra a Clean Architecture, porque:

- Domínio não conhece Spring, nem Email.
- Use case não conhece Spring, nem listener.
- Infraestrutura se adapta ao domínio (Ports & Adapters):
    - Implementa `DomainEventPublisher`.
    - Implementa listeners que reagem aos eventos.

---

## 8. Como isso se encaixa na ideia de callback/listener

- **Listener** = função/método que você registra e que **será chamado depois** quando algo acontecer → **callback**.
- **Evento de domínio** = “mensagem” que diz “aconteceu isso no domínio”.
- **Publisher** = quem dispara o evento.
- **Callback** (listener) = quem reage ao evento.

Lembra da frase:

> “Quando terminar, me chama de volta”?

Aqui é:

> “Quando uma Order for criada, execute esse código (listener) aqui.”

---

## 9. Variações avançadas (se quiser ir além depois)

- Listener na **camada de aplicação** para orquestrar outros use cases (ex: OrderCreated → GenerateInvoice).
- Listener na **infraestrutura** para:
    - publicar em Kafka/Rabbit
    - escrever em outro banco
    - fazer integração com terceiros
- Disparar eventos **sincronos** (dentro da transação)
- Disparar eventos **assíncronos** (fora da transação, via mensageria)
- Usar um `DomainEventDispatcher` próprio, sem Spring, pra ficar 100% agnóstico de framework.