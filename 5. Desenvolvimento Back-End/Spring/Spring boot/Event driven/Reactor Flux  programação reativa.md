## 1. O que é o Project Reactor?

**Project Reactor** é uma biblioteca Java para **programação reativa** baseada na especificação **Reactive Streams**.

Principais características:

- Assíncrono
    
- Não bloqueante (non-blocking)
    
- Trabalha com **fluxos de dados** (streams) ao longo do tempo
    
- Lida com **backpressure** (quando o consumidor não aguenta o ritmo do produtor)
    
- É a base do **Spring WebFlux** (`Mono`, `Flux` em tudo)
    

---

## 2. Tipos principais: `Flux` e `Mono`

No Reactor, você trabalha principalmente com dois tipos:

- **`Mono<T>`** → 0 ou 1 elemento  
    Ex.: resposta de um GET /user/1
    
- **`Flux<T>`** → 0..N elementos  
    Ex.: lista de usuários, stream de mensagens, eventos, etc.
    

Ambos são **Publishers** (reactive-streams): alguém produz, alguém se inscreve.

---

## 3. Exemplo básico com `Flux`

Vamos pegar uma lista de números, filtrar pares, elevar ao quadrado e imprimir:

`import reactor.core.publisher.Flux;  public class ReactorExemplo01 {     public static void main(String[] args) {         Flux<Integer> numeros = Flux.range(1, 10); // 1 a 10          numeros             .filter(n -> n % 2 == 0)  // mantém só pares             .map(n -> n * n)          // quadrado             .subscribe(n -> System.out.println("Valor: " + n));     } }`

Saída:

`Valor: 4 Valor: 16 Valor: 36 Valor: 64 Valor: 100`

💡 Perceba o estilo: **declara o pipeline** (`filter` → `map`) e, no final, chama `subscribe`.

> Sem `subscribe()`, nada acontece. É lazy.

---

## 4. Exemplo com `Mono`

`import reactor.core.publisher.Mono;  public class ReactorExemplo02 {     public static void main(String[] args) {         Mono<String> mono = Mono.just("Edson");          mono             .map(String::toUpperCase)             .subscribe(nome -> System.out.println("Olá, " + nome));     } }`

Saída:

`Olá, EDSON`

---

## 5. Assíncrono e não bloqueante na prática

Vamos simular algo “temporal” com `delayElements`:

`import reactor.core.publisher.Flux; import java.time.Duration;  public class ReactorExemplo03 {     public static void main(String[] args) throws InterruptedException {         Flux.interval(Duration.ofSeconds(1))      // emite 0,1,2,3...             .take(5)                              // só 5 elementos             .subscribe(n -> System.out.println("Tick: " + n));          System.out.println("Depois do subscribe...");          // só pra segurar a main viva até acabar o fluxo         Thread.sleep(6000);     } }`

Saída (algo assim):

`Depois do subscribe... Tick: 0 Tick: 1 Tick: 2 Tick: 3 Tick: 4`

Aqui:

- `Flux.interval` emite valores em outra thread;
    
- `subscribe` registra o consumidor;
    
- sua main termina se você não segurar com `sleep` (em app real, o contexto do Spring segura).
    

---

## 6. Comparando com Java Streams

**Java Streams (`java.util.stream`)**:

- Pensados para **coleções em memória**
    
- Processamento **sincrono**
    
- Uma vez consumido, acabou
    

**Reactor (`Flux` / `Mono`)**:

- Pensado para **dados ao longo do tempo** (eventos, requisições, IO)
    
- Processamento **assíncrono/non-blocking**
    
- Pode representar:
    
    - resultado de HTTP
        
    - resposta de DB reativo
        
    - fila de mensagens
        
    - timers, etc.
        

Você pode pensar assim:

> `Stream<T>` = coleção “parada”  
> `Flux<T>` = “rio” de dados fluindo

---

## 7. Exemplo mais próximo de regra de negócio

### Cenário: você quer buscar pedidos, filtrar e transformar

`import reactor.core.publisher.Flux;  public class PedidoServiceReativo {      public Flux<Pedido> buscarPedidos() {         return Flux.just(                 new Pedido("PED-1", 50.0),                 new Pedido("PED-2", 200.0),                 new Pedido("PED-3", 120.0)         );     }      public static void main(String[] args) {         PedidoServiceReativo service = new PedidoServiceReativo();          service.buscarPedidos()             .filter(p -> p.getValor() > 100)       // só pedidos > 100             .map(p -> "Pedido " + p.getCodigo() + " valor: " + p.getValor())             .subscribe(System.out::println);     } }  class Pedido {     private final String codigo;     private final double valor;      public Pedido(String codigo, double valor) {         this.codigo = codigo;         this.valor = valor;     }      public String getCodigo() { return codigo; }     public double getValor() { return valor; } }`

Saída:

`Pedido PED-2 valor: 200.0 Pedido PED-3 valor: 120.0`

---

## 8. Comportamento importante: nada acontece sem `subscribe`

Por padrão:

- `Flux` / `Mono` só começam a “rodar” quando alguém **se inscreve** (`subscribe`).
    
- O `subscribe` pode ter vários callbacks:
    

`flux.subscribe(     valor -> System.out.println("onNext: " + valor),        // sucesso     erro -> System.err.println("onError: " + erro),         // erro     () -> System.out.println("onComplete")                  // fim );`

---

## 9. Ligando isso com Event-Driven

Você pode usar `Flux` como uma **fonte contínua de eventos**.

Por exemplo, com `Sinks` (fonte manual):

`import reactor.core.publisher.Flux; import reactor.core.publisher.Sinks;  public class ReactorEventos {      public static void main(String[] args) {         Sinks.Many<String> sink = Sinks.many().multicast().onBackpressureBuffer();         Flux<String> flux = sink.asFlux();          flux.subscribe(msg -> System.out.println("Listener 1: " + msg));         flux.subscribe(msg -> System.out.println("Listener 2: " + msg));          sink.tryEmitNext("Evento 1");         sink.tryEmitNext("Evento 2");         sink.tryEmitComplete();     } }`

Saída:

`Listener 1: Evento 1 Listener 2: Evento 1 Listener 1: Evento 2 Listener 2: Evento 2`

Isso é basicamente **pub/sub em memória** de forma reativa.

---

## 10. Onde Reactor brilha de verdade

Principalmente quando você combina com:

- **Spring WebFlux**: controllers retornando `Mono<Response>` ou `Flux<Item>`
    
- **Banco reativo** (R2DBC, Mongo Reactive)
    
- **Mensageria reativa** (Kafka + Reactor, por exemplo)
    
- Integrações em que você não quer bloquear threads
    

Exemplo de método em service Spring WebFlux:

`public Mono<Pedido> criarPedido(PedidoRequest request) {     return validar(request)         .flatMap(this::salvarNoBanco)   // retorna Mono<Pedido>         .doOnNext(this::publicarEvento); // dispara evento reativo }`