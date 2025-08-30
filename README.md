
### Projeto: Validador de Sintaxe para "SimpleMarkup" *.sm

**O Conceito:**
Vamos considerar uma linguagem de marcação simples, que chamaremos de "SimpleMarkup" com a extensão sm. Ela usa tags com `<` e `>` para definir blocos, similar ao HTML. O objetivo do projeto é criar um programa que lê um arquivo `.sm` (SimpleMarkup) e diz se a sua sintaxe está correta ou não.

**Exemplo de um arquivo `documento.sm` VÁLIDO:**
```
<documento>
  <titulo>Meu Primeiro Documento</titulo>
  <corpo>
    <paragrafo>Este é um teste.</paragrafo>
    <paragrafo>As tags devem ser aninhadas corretamente.</paragrafo>
  </corpo>
</documento>
```

**Exemplo de um arquivo `documento.sm` INVÁLIDO:**
```
<documento>
  <titulo>Título Incorreto</corpo>  // Erro: tag de fechamento não corresponde
</documento>
```
ou
```
<documento>
  <paragrafo>Aninhamento quebrado.</documento> // Erro: </paragrafo> esperado antes de </documento>
</paragrafo>
```

O projeto pode ser quebrado em dois componentes principais e um projeto que vai usar os 2 componentes.
Os 3 projetos serão desenvolvidos por grupos diferentes: o **Analisador Léxico (Tokenizer)**, o **Analisador Sintático (Parser)** e o projeto principal (main).

---

### Divisão de Tarefas para os Grupos


#### **Tarefa para o Grupo 1: O Analisador Léxico (Tokenizer)**

*   **Missão:** Criar uma função ou classe em Python que lê o conteúdo de um arquivo `.sm` como uma string e a transforma em uma lista de "tokens".
*   **O que é um Token?** É uma unidade lógica da linguagem. Para o SimpleMarkup, os tokens seriam:
    *   `TAG_ABERTURA` (com o nome da tag, e.g., `documento`)
    *   `TAG_FECHAMENTO` (com o nome da tag, e.g., `documento`)
    *   `TEXTO` (o conteúdo entre as tags)
*   **Entrada da Função:** A string `<titulo>Meu Título</titulo>`
*   **Saída da Função (uma lista de tokens):**
    ```
    [
        ('TAG_ABERTURA', 'titulo'),
        ('TEXTO', 'Meu Título'),
        ('TAG_FECHAMENTO', 'titulo')
    ]
    ```
*   **Técnica Utilizada:** Dica usar **Expressões Regulares (Regex)**. 
Criar um padrão que consiga encontrar `<tag>`, `</tag>` e o texto no meio.
*   **Entregável:** 
Quatro arquivos de lista de tokens semelhante ao exemplo acima, dois válidos e dois inválidos:
Um arquivo `lexer.py` com uma função `tokenize(text)`.
Um arquivo `readme.md` com instruções de como rodar e 3 arquivos .sm válidos e 3 inválidos.


#### **Tarefa para o Grupo 2: O Analisador Sintático (Parser)**

*   **Missão:** Criar uma função ou classe que recebe a lista de tokens gerada pelo Grupo 1 e usa uma **pilha** para verificar se a sintaxe está correta.
*   **Lógica Principal (O Algoritmo da Pilha):**
    1.  Crie uma pilha vazia.
    2.  Itere sobre a lista de tokens:
        *   Se o token for uma `TAG_ABERTURA` (e.g., `('TAG_ABERTURA', 'titulo')`), **empilhe** o nome da tag (e.g., `'titulo'`).
        *   Se o token for uma `TAG_FECHAMENTO` (e.g., `('TAG_FECHAMENTO', 'titulo')`):
            *   Verifique se a pilha não está vazia. Se estiver, é um erro (tag de fechamento sem abertura).
            *   **Desempilhe** o último item.
            *   Compare o item desempilhado com o nome da tag de fechamento. Se não forem iguais, é um erro (tags não correspondem).
        *   Ignore os tokens de `TEXTO`.
    3.  Após iterar por todos os tokens, verifique se a pilha está vazia. Se não estiver, é um erro (uma ou mais tags foram abertas, mas nunca fechadas).
    4.  Se nenhum erro foi encontrado, a sintaxe é válida!
*   **Técnica Utilizada:** Implementação direta de um **Autômato de Pilha**.
*   **Entregável:** 
Um arquivo `parser.py` com uma função `parse(tokens)` que retorna `True` (válido) ou `False` (inválido) e talvez uma mensagem de erro.
Um arquivo `readme.md` com instruções de como rodar e 3 exemplos de tokens válidos e 3 exemplos de tokens inválidos.


#### **Tarefa para o Grupo 3: O projeto principal

Depois que os grupos finalizarem suas partes, a integração é simples, abaixo segue um exemplo:

**Arquivo `main.py`:**
```python
from lexer import tokenize
from parser import parse

def validar_arquivo(nome_arquivo):
    try:
        with open(nome_arquivo, 'r', encoding='utf-8') as f:
            conteudo = f.read()
        
        print(f"Validando o arquivo: {nome_arquivo}")
        
        # Passo 1: Usar o trabalho do Grupo 1
        tokens = tokenize(conteudo)
        print("Tokens gerados:", tokens)
        
        # Passo 2: Usar o trabalho do Grupo 2
        if parse(tokens):
            print("Resultado: A sintaxe do arquivo é VÁLIDA. ✅")
        else:
            print("Resultado: A sintaxe do arquivo é INVÁLIDA. ❌")
            
    except FileNotFoundError:
        print(f"Erro: Arquivo '{nome_arquivo}' não encontrado.")

# --- Execução ---
if __name__ == "__main__":
    validar_arquivo("documento_valido.sm")
    print("-" * 20)
    validar_arquivo("documento_invalido.sm")
```

Este é um exemplo apenas, podem aprimorar e/ou otimizar o código.
