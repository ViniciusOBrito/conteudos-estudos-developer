# **1. C# – Fundamentos da Linguagem**

## **1. Variáveis e Tipos de Dados**

### 1.1. Tipos de Dados Primitivos

- Integrais (`int`, `long`, `short`, `byte`)
    
- Ponto flutuante (`float`, `double`, `decimal`)
    
- Caractere (`char`)
    
- Booleano (`bool`)
    

### 1.2. Tipos de Dados por Referência e Personalizados

#### 1.2.1. Classes

#### 1.2.2. Structs

#### 1.2.3. Interfaces

#### 1.2.4. Enums

#### 1.2.5. Records

### 1.3. Tipos Anuláveis (Nullable)

#### 1.3.1. Tipos de valor anuláveis (`int?`)

#### 1.3.2. Nullable Reference Types (`string` vs `string?`)

#### 1.3.3. Análises e avisos do compilador

### 1.4. Operadores

#### 1.4.1. Aritméticos

#### 1.4.2. Lógicos

#### 1.4.3. Relacionais

#### 1.4.4. Operadores nulos (`??`, `?.`, `!`)

### 1.5. Conversão de Tipos

#### 1.5.1. Conversão implícita e explícita (cast)

#### 1.5.2. Classe `Convert`

#### 1.5.3. `Parse` e `TryParse`

---

## **2. Estruturas de Controle de Fluxo**

### 2.1. Condicionais

#### 2.1.1. `if`

#### 2.1.2. `if / else`

#### 2.1.3. `switch`

#### 2.1.4. `switch expression`

### 2.2. Laços de Repetição

#### 2.2.1. `for`

#### 2.2.2. `while`

#### 2.2.3. `do…while`

#### 2.2.4. `foreach`

### 2.3. Controle de Fluxo

#### 2.3.1. `break`

#### 2.3.2. `continue`

#### 2.3.3. `return`

---

## **3. Métodos e Funções**

### 3.1. Declaração de Métodos

### 3.2. Métodos com Retorno

### 3.3. Métodos `void`

### 3.4. Parâmetros

#### 3.4.1. Parâmetros por valor

#### 3.4.2. `ref`, `out`, `in`

#### 3.4.3. Parâmetros opcionais

#### 3.4.4. Named Parameters

### 3.5. Métodos Recursivos

### 3.6. Métodos Estáticos x Métodos de Instância

---

## **4. Arrays e Estruturas Sequenciais**

### 4.1. Declaração e Inicialização

### 4.2. Arrays Multidimensionais

### 4.3. Arrays Irregulares (Jagged Arrays)

### 4.4. Operações com Arrays

#### 4.4.1. Iteração

#### 4.4.2. Ordenação

#### 4.4.3. Busca

---

## **5. Classes e Objetos**

### 5.1. Declaração de Classes

### 5.2. Propriedades

#### 5.2.1. Auto-properties

#### 5.2.2. Propriedades somente leitura

#### 5.2.3. Propriedades imutáveis

### 5.3. Métodos

#### 5.3.1. Sobrecarga de Métodos

#### 5.3.2. Retorno de Objetos

### 5.4. Construtores

#### 5.4.1. Construtor padrão

#### 5.4.2. Sobrecarga de Construtores

### 5.5. Palavra-chave `this`

### 5.6. Sobrecarga de Operadores

---

## **6. Herança**

### 6.1. Classes Base e Derivadas

### 6.2. Palavra-chave `base`

### 6.3. Ordem de Execução de Construtores

### 6.4. Métodos `virtual`, `override`

### 6.5. Uso de `sealed`

---

## **7. Polimorfismo**

### 7.1. Polimorfismo de Sobrecarga

### 7.2. Polimorfismo de Substituição

---

## **8. Encapsulamento**

### 8.1. Modificadores de Acesso

- `public`
    
- `private`
    
- `protected`
    
- `internal`
    
- `protected internal`
    
- `private protected`
    

### 8.2. Propriedades (`get` / `set`)

### 8.3. Indexadores

---

## **9. Delegates e Eventos**

### 9.1. Delegates

#### 9.1.1. Delegate customizado

#### 9.1.2. Multicast Delegate

#### 9.1.3. `Func`, `Action`, `Predicate`

### 9.2. Eventos

#### 9.2.1. Declaração de Eventos

#### 9.2.2. Assinatura e Disparo

#### 9.2.3. Padrão Observer

---

## **10. Interfaces**

### 10.1. Declaração

### 10.2. Implementação

### 10.3. Múltiplas Interfaces

### 10.4. Métodos Default

---

## **11. Tratamento de Exceções**

### 11.1. `try / catch / finally`

### 11.2. Lançamento de Exceções

#### 11.2.1. `throw`

### 11.3. Hierarquia de Exceções

### 11.4. Exceções Personalizadas

### 11.5. Inner Exception

### 11.6. Filtros de Exceção (`when`)

---

## **12. Coleções**

### 12.1. `List<T>`

### 12.2. `Dictionary<TKey, TValue>`

### 12.3. `LinkedList<T>`

### 12.4. `Queue<T>`

### 12.5. `Stack<T>`

### 12.6. `HashSet<T>`

### 12.7. Coleções Concorrentes

- `ConcurrentDictionary`
    
- `ConcurrentQueue`
    
- `ConcurrentBag`
    

---

## **13. LINQ**

### 13.1. Conceitos

#### 13.1.1. Execução tardia (Deferred execution)

#### 13.1.2. Execução imediata

### 13.2. Operadores

#### 13.2.1. `Where`

#### 13.2.2. `Select`

#### 13.2.3. `GroupBy`

#### 13.2.4. `OrderBy`

### 13.3. Operadores de Conjunto

#### 13.3.1. `Union`

#### 13.3.2. `Intersect`

#### 13.3.3. `Except`

---

## **14. Expressões Lambda**

### 14.1. Sintaxe

### 14.2. Uso com Delegates

### 14.3. Uso com LINQ

---

## **15. Programação Assíncrona**

### 15.1. `async / await`

### 15.2. `Task` e `ValueTask`

### 15.3. Cancelamento (`CancellationToken`)

---

## **16. Concorrência e Paralelismo**

### 16.1. Concorrência x Paralelismo

### 16.2. `lock`

### 16.3. `Monitor`

### 16.4. `Semaphore`

### 16.5. `SemaphoreSlim`

---

## **17. Gerenciamento de Memória**

### 17.1. Stack x Heap

### 17.2. Value Types x Reference Types

### 17.3. Garbage Collector (.NET)

### 17.4. Imutabilidade

### 17.5. Pooling de Objetos

### 17.6. Tipos Avançados de Memória

#### 17.6.1. `Span<T>`

#### 17.6.2. `ReadOnlySpan<T>`

#### 17.6.3. `Memory<T>`

---

## **18. Datas, Horários e Tempo**

### 18.1. `DateTime`

### 18.2. `DateTimeOffset`

### 18.3. UTC x Local

### 18.4. TimeZone

---

## **19. Serialização**

### 19.1. Serialização JSON

### 19.2. Custom Converters

### 19.3. Versionamento de Contratos

---

## **20. Logging e Diagnóstico**

### 20.1. Conceitos de Logging

### 20.2. Níveis de Log

### 20.3. Correlação de Eventos

---

## **21. Testes em C#**

### 21.1. Testes Unitários

### 21.2. Mocking

### 21.3. Testabilidade de Código

---

# **22. C# – Manipulação de Arquivos**

## **22.1. Leitura e Escrita**

### 22.1.1. Arquivos de Texto

### 22.1.2. Arquivos Binários

## **22.2. Diretórios**

### 22.2.1. Criação e Remoção

### 22.2.2. Navegação

### 22.2.3. Verificação

### 22.2.4. Listagem

## **22.3. XML**

### 22.3.1. Leitura

### 22.3.2. Escrita

### 22.3.3. Modificação

### 22.3.4. Validação

### 22.3.5. Transformações

## **22.4. JSON**

### 22.4.1. Leitura

### 22.4.2. Escrita

### 22.4.3. Modificação

### 22.4.4. Validação

### 22.4.5. Serialização de Objetos

---

# **23. C# – Manipulação de Dados**

## **23.1. Conceitos de Banco de Dados**

### 23.1.1. Relacional

### 23.1.2. Transações

### 23.1.3. Normalização

## **23.2. ADO.NET**

### 23.2.1. Criação de Conexões

### 23.2.2. Execução de Consultas

### 23.2.3. Atualização de Dados

## **23.3. ORM**

### 23.3.1. Conceito de ORM

### 23.3.2. Entity Framework (fundamentos)

> _Integrações com ASP.NET Core ficam fora deste escopo._