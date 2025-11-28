## 1. O problema que ele resolve

Imagina esse cenário:

```java
@Service
public class PedidoService {

    private final ApplicationEventPublisher publisher;
    private final PedidoRepository pedidoRepository;

    public PedidoService(ApplicationEventPublisher publisher, PedidoRepository pedidoRepository) {
        this.publisher = publisher;
        this.pedidoRepository = pedidoRepository;
    }

    @Transactional
    public void criarPedido(Pedido pedido) {
        pedidoRepository.save(pedido);

        // dispara evento
        publisher.publishEvent(new PedidoCriadoEvent(pedido.getId()));
    }
}

```

E um listener assim:

```java
@Component
public class PedidoEventHandler {

    @EventListener
    public void aoCriarPedido(PedidoCriadoEvent event) {
        // Envia e-mail, chama outro sistema, publica em Kafka, etc
        System.out.println("Enviando e-mail do pedido " + event.getPedidoId());
    }
}

```

💥 Problema:

- O listener roda **DENTRO da transação**.
    
- Se a transação der **rollback** (ex: erro ao salvar no banco depois),
    
    **o e-mail já foi enviado**, ou a mensagem já foi para Kafka, Rabbit etc.
    

Ou seja: **efeito colateral externo sem garantia de commit**.

É aí que entra o:

---

## 2. O que é `@TransactionalEventListener`?

É uma versão “inteligente” do `@EventListener` que sabe respeitar o ciclo de vida da transação.

Ele permite dizer:

> “Rodar esse listener apenas depois do commit da transação”
> 
> ou
> 
> “Rodar apenas no rollback”
> 
> ou
> 
> “Rodar antes do commit”

Sintaxe básica:

```java
@TransactionalEventListener
public void handler(PedidoCriadoEvent event) {
    ...
}

```

Por padrão, ele roda **DEPOIS DO COMMIT** (phase = `AFTER_COMMIT`).

---

## 3. Fases do `@TransactionalEventListener`

Você pode configurar o momento em que o listener é disparado usando o atributo `phase`.

```java
@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
public void handler(PedidoCriadoEvent event) { ... }

```

As fases:

|Phase|Quando executa|Uso típico|
|---|---|---|
|`BEFORE_COMMIT`|Antes da transação ser commitada|Validar algo extra, preparar cache|
|`AFTER_COMMIT` (default)|**Depois** do commit bem-sucedido|Enviar e-mail, publicar em fila, notificar outros sistemas|
|`AFTER_ROLLBACK`|Quando a transação faz rollback|Compensações, log de erro específico|
|`AFTER_COMPLETION`|Sempre no fim (commit ou rollback)|Limpeza de recursos, logs gerais|

Na prática, **90% dos casos** são `AFTER_COMMIT`.

---

## 4. Exemplo completo na prática

### 4.1. Evento de domínio

```java
public class PedidoCriadoEvent {

    private final Long pedidoId;

    public PedidoCriadoEvent(Long pedidoId) {
        this.pedidoId = pedidoId;
    }

    public Long getPedidoId() {
        return pedidoId;
    }
}

```

---

### 4.2. Service que dispara o evento dentro da transação

```java
@Service
public class PedidoService {

    private final PedidoRepository pedidoRepository;
    private final ApplicationEventPublisher publisher;

    public PedidoService(PedidoRepository pedidoRepository,
                         ApplicationEventPublisher publisher) {
        this.pedidoRepository = pedidoRepository;
        this.publisher = publisher;
    }

    @Transactional
    public Long criarPedido(Pedido pedido) {
        pedidoRepository.save(pedido);

        // evento é publicado ainda DENTRO da transação
        publisher.publishEvent(new PedidoCriadoEvent(pedido.getId()));

        // se der rollback depois daqui, o listener AFTER_COMMIT NÃO roda
        return pedido.getId();
    }
}

```

---

### 4.3. Listener transacional

```java
import org.springframework.stereotype.Component;
import org.springframework.transaction.event.TransactionPhase;
import org.springframework.transaction.event.TransactionalEventListener;

@Component
public class PedidoTransactionalHandler {

    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void aoCriarPedido(PedidoCriadoEvent event) {
        System.out.println("[AFTER_COMMIT] Transação OK. Enviando e-mail do pedido: " + event.getPedidoId());
        // aqui você pode:
        // - enviar e-mail
        // - publicar em RabbitMQ/Kafka
        // - chamar outro microserviço
    }

    @TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)
    public void aoFalharCriacaoPedido(PedidoCriadoEvent event) {
        System.out.println("[AFTER_ROLLBACK] Falha na transação. Logando erro do pedido: " + event.getPedidoId());
    }
}

```

**Comportamento:**

- Se o método `criarPedido()` finalizar com commit:
    - roda apenas o método `aoCriarPedido` (AFTER_COMMIT)
- Se der exception e rollback:
    - roda apenas o `aoFalharCriacaoPedido` (AFTER_ROLLBACK)

É esse o pulo do gato: o listener **respeita o resultado da transação**.

---

## 5. Diferença na prática: `@EventListener` x `@TransactionalEventListener`

### `@EventListener`

- Listener executa **imediatamente** quando o evento é publicado
- Se estiver dentro de uma transação:
    - roda **antes do commit**
    - não sabe se vai ter commit ou rollback

### `@TransactionalEventListener(AFTER_COMMIT)`

- Evento é **registrado** durante a transação
- O handler roda **só depois** do commit bem-sucedido

Em código:

```java
@EventListener
public void handlerNormal(PedidoCriadoEvent e) {
    // roda na hora que publishEvent é chamado
}

@TransactionalEventListener
public void handlerTransacional(PedidoCriadoEvent e) {
    // roda depois do commit da transação
}

```

---

## 6. Com DDD + Domain Events (como você já viu)

Combina muito bem com o esquema que te mostrei de Aggregates + Domain Events:

- Aggregate (`Pedido`) registra `PedidoCriadoEvent` internamente
- Application Service salva o aggregate e chama um `DomainEventPublisher`
- Internamente isso vira um `ApplicationEventPublisher` do Spring
- Os handlers usam `@TransactionalEventListener(AFTER_COMMIT)`

Isso garante:

- **Regra de negócio no domínio**
- **Eventos coerentes com o estado do banco** (nada de mandar evento de algo que não foi persistido)
- **Side-effects externos só depois do commit**

---

## 7. Pontos importantes / Gotchas 👀

1. **Precisa ter transação ativa**
    
    - Se você chamar `publishEvent` fora de um método `@Transactional`,
        
        o `@TransactionalEventListener` roda na hora mesmo (sem fase).
        
2. **Mesma thread**
    
    - O evento transacional é associado à transação atual da thread.
    - Se você rodar coisas com `@Async` antes da publicação, cuidado.
3. **Combinar com `@Async` no listener**
    
    - Dá pra fazer:
        
        ```java
        @Async
        @TransactionalEventListener(phase = AFTER_COMMIT)
        public void handler(...) {...}
        
        ```
        
    - Isso garante:
        
        - Só dispara **depois do commit**
        - E o processamento é assíncrono (em outra thread)
4. **Propagação de transação**
    
    - Se você usar `@Transactional(propagation = REQUIRES_NEW)` em algum lugar interno,
        
        o evento vai estar associado àquela transação, não necessariamente à externa.
        

---

## 8. Quando você deve usar `@TransactionalEventListener`?

Use quando:

- Está disparando **eventos de domínio** a partir de métodos transacionais.
- Vai fazer **efeitos colaterais externos** (e-mail, mensageria, integrações).
- Não quer risco de:
    - commit falhar, mas e-mail já ter sido enviado
    - rollback, mas mensagem já ter sido publicada no Rabbit/Kafka

É praticamente **obrigatório** em DDD + eventos + persistência.