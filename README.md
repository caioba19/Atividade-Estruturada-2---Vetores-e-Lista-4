# 📚 Atividade Estruturada 2 — Funções e Operações com Listas Lineares Sequenciais

> **Disciplina:** Estrutura de Dados | **Linguagem:** C  
> **Aluno:** Caio Vinicius | **Instituição:** Centro Universitário Jorge Amado

---

## Questão 1 — Análise do Programa

### Código analisado

```c
#include <stdio.h>

void Teste1(int);
void Teste2(int *);
int  Teste3(int);

int x = 20; // variável GLOBAL

int main(void) {
    int numero = 10, outroNumero;
    Teste1(numero);
    printf("Valor de numero (apos Teste1) = %d\n", numero);
    printf("X = %d\n", x);
    Teste2(&numero);
    printf("Valor de numero (apos Teste2) = %d\n", numero);
    printf("X = %d\n", x);
    outroNumero = Teste3(numero);
    printf("Valor de outro numero (apos Teste3) = %d\n", outroNumero);
    printf("X = %d\n", x);
    return 0;
}

void Teste1(int numero) {
    numero = numero + x;
    x++;
}

void Teste2(int *numero) {
    int valor = 100;
    *numero = *numero + valor;
    x++;
}

int Teste3(int n) {
    int valor = 200;
    n = n + valor;
    x--;
    return n;
}
```

---

### a) Variáveis Globais e Locais

**Variável Global:**
| Variável | Tipo | Escopo |
|----------|------|--------|
| `x = 20` | `int` | Global — acessível e modificável em todo o programa durante toda a execução |

**Variáveis Locais:**
| Variável | Função | Escopo / Tempo de Vida |
|----------|--------|------------------------|
| `numero` | `main` | Do início da `main` até `return 0` |
| `outroNumero` | `main` | Do início da `main` até `return 0` |
| `numero` (parâmetro) | `Teste1` | Criada ao chamar `Teste1`, destruída ao retornar |
| `numero` (ponteiro) | `Teste2` | Criada ao chamar `Teste2`, destruída ao retornar |
| `valor` | `Teste2` | Criada dentro de `Teste2`, destruída ao retornar |
| `n` (parâmetro) | `Teste3` | Criada ao chamar `Teste3`, destruída ao retornar |
| `valor` | `Teste3` | Criada dentro de `Teste3`, destruída ao retornar |

---

### b) Tipo de Passagem de Parâmetros

| Função | Passagem | Explicação |
|--------|----------|------------|
| `Teste1(int numero)` | **Por valor** | Uma cópia do argumento é criada. Alterações no parâmetro local **não** afetam a variável original em `main`. |
| `Teste2(int *numero)` | **Por referência (ponteiro)** | O endereço de `numero` é passado. Alterações via `*numero` modificam diretamente a variável original em `main`. |
| `Teste3(int n)` | **Por valor** | Uma cópia do argumento é criada. Alterações em `n` **não** refletem em `main`; o resultado é retornado via `return`. |

---

### c) Rastreamento Passo a Passo das Variáveis

| Momento / Evento | `x` (global) | `numero` (main) | `outroNumero` (main) | Obs. |
|------------------|:------------:|:---------------:|:--------------------:|------|
| Início de `main` | 20 | 10 | indefinido | Variáveis locais alocadas na pilha |
| Chamada `Teste1(10)` | 20 | 10 | — | Cópia `numero=10` criada para `Teste1` |
| Dentro `Teste1`: `numero = 10 + 20` | 20 | 10 | — | Variável **local** de `Teste1` vira 30; `main.numero` intocado |
| Dentro `Teste1`: `x++` | **21** | 10 | — | Global alterada |
| Retorno de `Teste1` | 21 | 10 | — | ⚠️ `numero` e `x` locais de `Teste1` **liberados** |
| `printf` após `Teste1` | 21 | **10** | — | `numero` não mudou (passagem por valor) |
| Chamada `Teste2(&numero)` | 21 | 10 | — | Endereço de `main.numero` passado |
| Dentro `Teste2`: `valor = 100` criado | 21 | 10 | — | Local `valor` alocado |
| Dentro `Teste2`: `*numero = 10 + 100` | 21 | **110** | — | `main.numero` alterado via ponteiro |
| Dentro `Teste2`: `x++` | **22** | 110 | — | Global alterada |
| Retorno de `Teste2` | 22 | 110 | — | ⚠️ Ponteiro `numero` e `valor` locais **liberados** |
| `printf` após `Teste2` | 22 | **110** | — | `numero` foi modificado pela referência |
| Chamada `Teste3(110)` | 22 | 110 | — | Cópia `n=110` criada para `Teste3` |
| Dentro `Teste3`: `valor = 200` criado | 22 | 110 | — | Local `valor` alocado |
| Dentro `Teste3`: `n = 110 + 200` | 22 | 110 | — | `n` vira 310; `main.numero` intocado |
| Dentro `Teste3`: `x--` | **21** | 110 | — | Global decrementada |
| Retorno de `Teste3` → `outroNumero = 310` | 21 | 110 | **310** | ⚠️ `n` e `valor` locais de `Teste3` **liberados** |
| `printf` após `Teste3` | 21 | 110 | **310** | |
| Fim `main` / `return 0` | 21 | — | — | ⚠️ `numero` e `outroNumero` **liberados** |

---

### d) Saída Impressa na Tela

```
Valor de numero (apos Teste1) = 10
X = 21
Valor de numero (apos Teste2) = 110
X = 22
Valor de outro numero (apos Teste3) = 310
X = 21
```

**Justificativa:**
- **Após `Teste1`:** `numero` continua `10` porque foi passado **por valor** (cópia). `x` foi incrementado de 20 → 21.
- **Após `Teste2`:** `numero` passa a `110` (10 + 100) pois foi passado **por referência** (ponteiro). `x` foi incrementado de 21 → 22.
- **Após `Teste3`:** `outroNumero` recebe o valor de retorno `310` (110 + 200). `x` foi decrementado de 22 → 21.

---

## Questão 2 — Programa: Listas Lineares Sequenciais

### Funções implementadas

| Função | Descrição |
|--------|-----------|
| `inserirSemRepetir` | Insere elemento com validação: lista cheia, valor nulo e duplicidade |
| `listar` | Percorre e imprime todos os elementos |
| `intercalar` | Alterna elementos das duas listas em uma terceira |
| `intersecao` | Gera lista com elementos comuns às duas listas |
| `uniao` | Gera lista com todos os elementos distintos das duas listas |
| `removerPeloIndice` | Remove pelo índice, deslocando os posteriores |
| `buscarElemento` | Busca sequencial auxiliar — retorna índice ou `-1` |

---

### Código-Fonte Completo

```c
#include <stdio.h>

#define MAX 10

/* ─── Protótipos ─────────────────────────────────────────────────────── */
void inserirSemRepetir(int v[], int valor, int *pos, int max);
void listar(int v[], int qtd);
void intercalar(int v1[], int qtd1, int v2[], int qtd2,
                int resultado[], int *qtdResultado);
void intersecao(int v1[], int qtd1, int v2[], int qtd2,
                int resultado[], int *qtdResultado);
void uniao(int v1[], int qtd1, int v2[], int qtd2,
           int resultado[], int *qtdResultado);
void removerPeloIndice(int v[], int *quantidade, int indice);
int  buscarElemento(int v[], int qtd, int valor);

/* ─── main ───────────────────────────────────────────────────────────── */
int main(void) {
    int lista1[MAX], lista2[MAX];
    int qtd1 = 0, qtd2 = 0;
    int valor, i;

    /* a) Construção das listas */
    printf("=== Construção da Lista 1 ===\n");
    for (i = 0; i < MAX; i++) {
        printf("Elemento %d: ", i + 1);
        scanf("%d", &valor);
        inserirSemRepetir(lista1, valor, &qtd1, MAX);
    }

    printf("\n=== Construção da Lista 2 ===\n");
    for (i = 0; i < MAX; i++) {
        printf("Elemento %d: ", i + 1);
        scanf("%d", &valor);
        inserirSemRepetir(lista2, valor, &qtd2, MAX);
    }

    /* b) Impressão das listas */
    printf("\n=== Lista 1 ===\n");
    listar(lista1, qtd1);
    printf("\n=== Lista 2 ===\n");
    listar(lista2, qtd2);

    /* c) Intercalação */
    int listaIntercalada[MAX * 2];
    int qtdIntercalada = 0;
    intercalar(lista1, qtd1, lista2, qtd2, listaIntercalada, &qtdIntercalada);
    printf("\n=== Lista Intercalada (c) ===\n");
    listar(listaIntercalada, qtdIntercalada);

    /* d) Interseção */
    int listaIntersecao[MAX];
    int qtdIntersecao = 0;
    intersecao(lista1, qtd1, lista2, qtd2, listaIntersecao, &qtdIntersecao);
    printf("\n=== Interseção das Listas (d) ===\n");
    listar(listaIntersecao, qtdIntersecao);

    /* e) Impressão das listas c e d — já feita acima com listar() */

    /* f) União */
    int listaUniao[MAX * 2];
    int qtdUniao = 0;
    uniao(lista1, qtd1, lista2, qtd2, listaUniao, &qtdUniao);
    printf("\n=== União das Listas (f) ===\n");
    listar(listaUniao, qtdUniao);

    /* g) Remover por índice */
    int indice;
    printf("\nDigite o índice (0 a %d) do elemento a remover: ", qtdUniao - 1);
    scanf("%d", &indice);
    if (indice < 0 || indice >= qtdUniao) {
        printf("ERRO: Índice inválido!\n");
    } else {
        removerPeloIndice(listaUniao, &qtdUniao, indice);
        printf("\n=== Lista União após remoção (g) ===\n");
        listar(listaUniao, qtdUniao);
    }

    return 0;
}

/* ─── Funções ────────────────────────────────────────────────────────── */

/* Busca sequencial: retorna índice do valor ou -1 se não encontrado */
int buscarElemento(int v[], int qtd, int valor) {
    for (int i = 0; i < qtd; i++)
        if (v[i] == valor) return i;
    return -1;
}

/* a) Insere sem repetição, com validações */
void inserirSemRepetir(int v[], int valor, int *pos, int max) {
    if (valor == 0) {
        printf("ERRO: Valor zero não é permitido!\n");
        return;
    }
    if (*pos >= max) {
        printf("ERRO: Lista cheia (max = %d)!\n", max);
        return;
    }
    if (buscarElemento(v, *pos, valor) != -1) {
        printf("ERRO: Valor %d já existe na lista!\n", valor);
        return;
    }
    v[*pos] = valor;
    (*pos)++;
}

/* b) Percorre e imprime a lista */
void listar(int v[], int qtd) {
    if (qtd == 0) { printf("Lista vazia.\n"); return; }
    printf("[ ");
    for (int i = 0; i < qtd; i++) {
        printf("%d", v[i]);
        if (i < qtd - 1) printf(", ");
    }
    printf(" ]\n");
}

/* c) Intercala as duas listas alternando elementos */
void intercalar(int v1[], int qtd1, int v2[], int qtd2,
                int resultado[], int *qtdResultado) {
    int i = 0, j = 0, k = 0;
    while (i < qtd1 || j < qtd2) {
        if (i < qtd1) resultado[k++] = v1[i++];
        if (j < qtd2) resultado[k++] = v2[j++];
    }
    *qtdResultado = k;
}

/* d) Gera a interseção das duas listas */
void intersecao(int v1[], int qtd1, int v2[], int qtd2,
                int resultado[], int *qtdResultado) {
    *qtdResultado = 0;
    for (int i = 0; i < qtd1; i++)
        if (buscarElemento(v2, qtd2, v1[i]) != -1)
            resultado[(*qtdResultado)++] = v1[i];
}

/* f) Gera a união das duas listas (sem duplicatas) */
void uniao(int v1[], int qtd1, int v2[], int qtd2,
           int resultado[], int *qtdResultado) {
    *qtdResultado = 0;
    for (int i = 0; i < qtd1; i++)
        resultado[(*qtdResultado)++] = v1[i];
    for (int i = 0; i < qtd2; i++)
        if (buscarElemento(resultado, *qtdResultado, v2[i]) == -1)
            resultado[(*qtdResultado)++] = v2[i];
}

/* g) Remove elemento pelo índice, deslocando os posteriores */
void removerPeloIndice(int v[], int *quantidade, int indice) {
    for (int i = indice; i < *quantidade - 1; i++)
        v[i] = v[i + 1];
    (*quantidade)--;
}
```

---

### Explicação por Item

#### a) `inserirSemRepetir`
A função realiza **três verificações** antes de inserir:
1. **Valor zero:** inteiros não nulos são exigidos; zero é rejeitado com mensagem de erro.
2. **Lista cheia:** se `*pos >= max`, a inserção é impedida.
3. **Duplicidade:** `buscarElemento` percorre o vetor sequencialmente; se encontrar o valor, a inserção é ignorada com aviso.

Se todas as verificações passarem, o elemento é colocado em `v[*pos]` e `*pos` é incrementado **via ponteiro** para atualizar o chamador.

#### b) `listar`
Percorre o vetor de `0` até `qtd - 1` imprimindo cada elemento. Se `qtd == 0`, exibe `"Lista vazia."`.

#### c) Intercalação
Usa dois índices (`i` e `j`) que percorrem as listas simultaneamente. Em cada iteração insere primeiro um elemento de `v1` (se disponível) e depois um de `v2`.

> **Exemplo:** L1 = {10, 34, 5}, L2 = {4, 7, 9} → Intercalada = {10, **4**, 34, **7**, 5, **9**}

#### d) Interseção
Para cada elemento de L1, verifica se ele também existe em L2 usando `buscarElemento`. Se sim, é incluído no resultado. Garante apenas os elementos **comuns** às duas listas.

#### e) Impressão (c e d)
As listas intercalada e de interseção são impressas com a mesma função `listar`, reutilizando o código de percurso.

#### f) União
Copia todos os elementos de L1 para o resultado. Depois percorre L2: cada elemento só é adicionado se **ainda não estiver** no resultado (verificado por `buscarElemento`). Garante a propriedade de conjunto — sem duplicatas.

#### g) `removerPeloIndice`
A validação do índice é feita na `main` antes de chamar a função. Dentro da função, os elementos posteriores ao índice são deslocados uma posição à esquerda (`v[i] = v[i+1]`) e `*quantidade` é decrementado via ponteiro.

---

### Exemplo de Execução

```
Entrada: Lista 1 = {10, 34, 5, 8, 2}
         Lista 2 = {4, 7, 9, 34, 8}
```

```
=== Lista 1 ===
[ 10, 34, 5, 8, 2 ]

=== Lista 2 ===
[ 4, 7, 9, 34, 8 ]

=== Lista Intercalada (c) ===
[ 10, 4, 34, 7, 5, 9, 8, 34, 2, 8 ]

=== Interseção das Listas (d) ===
[ 34, 8 ]

=== União das Listas (f) ===
[ 10, 34, 5, 8, 2, 4, 7, 9 ]

Digite o índice (0 a 7) do elemento a remover: 2

=== Lista União após remoção (g) ===
[ 10, 34, 8, 2, 4, 7, 9 ]
```

---

> *Atividade Estruturada 2 — Estrutura de Dados em C*
