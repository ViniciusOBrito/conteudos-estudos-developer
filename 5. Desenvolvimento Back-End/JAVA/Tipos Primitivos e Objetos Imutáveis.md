Quando você usa `final` em tipos primitivos ou em objetos imutáveis, ele garante que o **valor** não será alterado.

### a) Tipos Primitivos (ex: `int`, `boolean`, `double`)

- **O que `final` protege:** O próprio valor.
    
- **Exemplo:**
    
    Java
    
    ```
    final int IDADE_FIXA = 30;
    // IDADE_FIXA = 31; // ERRO: O valor (30) não pode ser alterado.
    ```
    
    Neste caso, a variável **sempre** será 30.
    

### b) Objetos Imutáveis (ex: `String`, `Integer`)

- **O que `final` protege:** A **referência** (o lugar na memória) para o objeto. Como o objeto em si é imutável, o conteúdo interno também não pode ser alterado.
    
- **Exemplo:**
    
    Java
    
    ```
    final String NOME = "Carlos";
    // NOME = "João"; // ERRO: A referência não pode ser mudada para apontar para "João".
    
    // NOME.toUpperCase(); // Permitido, mas não altera o valor interno da String
    ```
    
    A variável `NOME` sempre apontará para o mesmo objeto `String` na memória (que contém "Carlos").
    

---

## 2. Objetos Mutáveis (Arrays e Coleções)

Quando você usa `final` em objetos que podem ser modificados (_mutáveis_), a regra do **`final`** se aplica **apenas à referência**, e **não** ao conteúdo interno.

### O que `final` protege (e o que não protege)

- **`final` protege o _Ponteiro_:** A variável `final` **sempre** apontará para o **mesmo objeto** na memória (o mesmo endereço).
    
- **`final` NÃO protege o _Conteúdo_:** Você **pode** alterar o estado interno (o conteúdo) do objeto para o qual a referência aponta.
    

### Exemplo

Imagine uma lista:

Java

```
public class ExemploMutavel {
    public static final List<String> CIDADES = new ArrayList<>();
}

// Em outro lugar do código:

// 1. Tentar mudar a referência (o ponteiro):
// ExemploMutavel.CIDADES = new LinkedList<>(); // ERRO! A referência é final.

// 2. Tentar mudar o conteúdo (o estado interno do objeto):
ExemploMutavel.CIDADES.add("São Paulo"); // OK! O conteúdo interno é mutável.
ExemploMutavel.CIDADES.add("Rio de Janeiro"); // OK! O conteúdo interno é mutável.

```

Neste exemplo, a variável **`CIDADES`** é **`final`**, o que significa que ela sempre apontará para a **mesma `ArrayList` inicial** na memória. No entanto, o objeto `ArrayList` é _mutável_, então você pode adicionar, remover ou modificar elementos **dentro** dele.

Em resumo:

|**Cenário**|**final garante que o Lugar na Memória (Referência)...**|**final garante que o Conteúdo Interno (Valor)...**|
|---|---|---|
|**Primitivos/Imutáveis**|Não pode ser mudado.|Não pode ser mudado.|
|**Objetos Mutáveis**|**Não pode ser mudado.**|**Pode ser mudado!**|