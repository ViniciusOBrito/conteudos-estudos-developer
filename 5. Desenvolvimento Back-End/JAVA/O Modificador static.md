O modificador `static` define o **escopo** da variável, transformando-a de um membro de instância para um membro de classe.

### Comportamento

|**Aspecto**|**Descrição**|
|---|---|
|**Escopo**|**Classe.** A variável pertence à própria classe, não a um objeto (instância) específico.|
|**Alocação**|Alocada apenas **uma vez** quando a classe é carregada pela JVM.|
|**Acesso**|Pode ser acessada **diretamente pela classe** (ex: `MinhaClasse.contador`), sem precisar criar uma instância.|
|**Ciclo de Vida**|Dura enquanto a classe estiver carregada. **Não é coletada** pelo Garbage Collector (GC), a menos que a classe seja descarregada.|
|**Mutabilidade**|**Mutável.** O valor pode ser alterado após a inicialização.|

### Exemplo

Java

```
public class Estatisticas {
    // Variável 'contador' é compartilhada por todas as instâncias
    public static int contador = 0;

    public Estatisticas() {
        contador++; // Acessa e modifica o valor único para todas as instâncias
    }
}
```

Se você criar 50 objetos `Estatisticas`, haverá 50 instâncias diferentes, mas **apenas uma** variável `contador` (que terá o valor 50).

---

## 🔒 2. O Modificador `final`

O modificador `final` define a **mutabilidade** (capacidade de ser alterado) da variável, transformando-a em uma constante.

### Comportamento

|**Aspecto**|**Descrição**|
|---|---|
|**Escopo**|**Instância.** A variável pertence ao objeto (instância) e é criada toda vez que um novo objeto é instanciado.|
|**Alocação**|Alocada toda vez que uma nova instância é criada. Cada instância tem sua própria cópia.|
|**Acesso**|Acessada **pela instância** (ex: `objeto.nome`).|
|**Ciclo de Vida**|Dura enquanto a instância for referenciada. **É elegível para coleta** pelo GC quando a instância deixa de ser usada.|
|**Mutabilidade**|**Imutável após a inicialização.** O valor deve ser definido no momento da declaração ou no construtor.|

### Exemplo

Java

```
public class Pessoa {
    // Variável 'id' é única para cada instância e não pode ser alterada
    public final int id;

    public Pessoa(int id) {
        this.id = id; // Inicializado no construtor
    }

    // public void setId(int novoId) { // ERRO! Não é permitido
    //     this.id = novoId;
    // }
}
```

Se você criar 50 objetos `Pessoa`, haverá 50 variáveis `id` diferentes, cada uma com seu próprio valor que não pode ser mudado.

---

## ⭐ 3. A Combinação `static final`

A combinação **`static final`** é usada para criar uma **Constante de Classe** — um valor que é **compartilhado** por todas as instâncias e **nunca pode ser alterado**.

### Comportamento

|**Aspecto**|**Descrição**|
|---|---|
|**Escopo**|**Classe.** (Devido ao `static`)|
|**Alocação**|Alocada **apenas uma vez** quando a classe é carregada.|
|**Acesso**|Acessada **diretamente pela classe** (ex: `Configuracoes.VERSAO`).|
|**Ciclo de Vida**|Dura enquanto a classe estiver carregada. **Não é coletada** pelo GC.|
|**Mutabilidade**|**Imutável após a inicialização.** (Devido ao `final`)|

### Exemplo (O Uso Comum para Constantes)

Java

```
public class Configuracoes {
    // Constante que nunca muda e é a mesma para toda a aplicação
    public static final String VERSAO = "1.0.2-BETA";
}
```

### Resumo da Diferença

|**Modificador**|**Escopo**|**Mutabilidade**|**Ciclo de Vida**|**Uso Comum**|
|---|---|---|---|---|
|**Nenhum**|Instância|Mutável|Vida do Objeto (Heap)|Variáveis de estado do objeto|
|**`final`**|Instância|Imutável|Vida do Objeto (Heap)|Parâmetros de _latch_ (dados definidos uma vez)|
|**`static`**|Classe|Mutável|Vida da Classe (Metaspace/Heap)|Variáveis de contagem ou estado global|
|**`static final`**|Classe|Imutável|Vida da Classe (Metaspace/Heap)|**Constantes** de aplicação (configurações fixas)|

Gostaria de ver algum exemplo prático de onde um desses modificadores é mais apropriado que os outros?