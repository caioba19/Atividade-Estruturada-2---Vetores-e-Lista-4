const {
  Document, Packer, Paragraph, TextRun, HeadingLevel,
  AlignmentType, LevelFormat, BorderStyle, WidthType,
  Table, TableRow, TableCell, ShadingType
} = require('docx');
const fs = require('fs');

// ─── helpers ───────────────────────────────────────────────────────────────
function h1(text) {
  return new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun(text)] });
}
function h2(text) {
  return new Paragraph({ heading: HeadingLevel.HEADING_2, children: [new TextRun(text)] });
}
function h3(text) {
  return new Paragraph({ heading: HeadingLevel.HEADING_3, children: [new TextRun(text)] });
}
function p(runs, opts = {}) {
  const children = Array.isArray(runs) ? runs : [new TextRun({ text: runs })];
  return new Paragraph({ children, spacing: { after: 100 }, ...opts });
}
function bold(text) { return new TextRun({ text, bold: true }); }
function normal(text) { return new TextRun({ text }); }
function code(text) {
  return new TextRun({ text, font: 'Courier New', size: 20 });
}
function codeBlock(lines) {
  return lines.map(line =>
    new Paragraph({
      children: [new TextRun({ text: line, font: 'Courier New', size: 18 })],
      spacing: { after: 0, before: 0 },
      indent: { left: 360 },
      border: { top: { style: BorderStyle.NONE }, bottom: { style: BorderStyle.NONE } },
      shading: { fill: 'F2F2F2', type: ShadingType.CLEAR }
    })
  );
}
function bullet(runs, level = 0) {
  const children = Array.isArray(runs) ? runs : [new TextRun({ text: runs })];
  return new Paragraph({
    children,
    numbering: { reference: 'bullets', level },
    spacing: { after: 80 }
  });
}
function spacer() {
  return new Paragraph({ children: [new TextRun('')], spacing: { after: 60 } });
}

// ─── QUESTÃO 1 ─────────────────────────────────────────────────────────────

const codigoFonte = [
  '#include <stdio.h>',
  '',
  'void Teste1(int);',
  'void Teste2(int *);',
  'int  Teste3(int);',
  '',
  'int x = 20;                  // variavel GLOBAL',
  '',
  'int main(void) {',
  '    int numero = 10, outroNumero;',
  '    Teste1(numero);',
  '    printf("Valor de numero (apos Teste1) = %d\\n", numero);',
  '    printf("X = %d\\n", x);',
  '    Teste2(&numero);',
  '    printf("Valor de numero (apos Teste2) = %d\\n", numero);',
  '    printf("X = %d\\n", x);',
  '    outroNumero = Teste3(numero);',
  '    printf("Valor de outro numero (apos Teste3) = %d\\n", outroNumero);',
  '    printf("X = %d\\n", x);',
  '    return 0;',
  '}',
  '',
  'void Teste1(int numero) {',
  '    numero = numero + x;',
  '    x++;',
  '}',
  '',
  'void Teste2(int *numero) {',
  '    int valor = 100;',
  '    *numero = *numero + valor;',
  '    x++;',
  '}',
  '',
  'int Teste3(int n) {',
  '    int valor = 200;',
  '    n = n + valor;',
  '    x--;',
  '    return n;',
  '}',
];

const q2codigo = [
  '#include <stdio.h>',
  '',
  '#define MAX 10',
  '',
  '/* Prototipos */  ',
  'void inserirSemRepetir(int v[], int valor, int *pos, int max);',
  'void listar(int v[], int qtd);',
  'void intercalar(int v1[], int qtd1, int v2[], int qtd2,',
  '                int resultado[], int *qtdResultado);',
  'void intersecao(int v1[], int qtd1, int v2[], int qtd2,',
  '                int resultado[], int *qtdResultado);',
  'void uniao(int v1[], int qtd1, int v2[], int qtd2,',
  '           int resultado[], int *qtdResultado);',
  'void removerPeloIndice(int v[], int *quantidade, int indice);',
  'int  buscarElemento(int v[], int qtd, int valor);',
  '',
  '/* ─── main ──────────────────────────────────────────────────────── */',
  'int main(void) {',
  '    int lista1[MAX], lista2[MAX];',
  '    int qtd1 = 0, qtd2 = 0;',
  '    int valor, i;',
  '',
  '    /* a) Construcao das listas */',
  '    printf("=== Construcao da Lista 1 ===\\n");',
  '    for (i = 0; i < MAX; i++) {',
  '        printf("Elemento %d: ", i + 1);',
  '        scanf("%d", &valor);',
  '        inserirSemRepetir(lista1, valor, &qtd1, MAX);',
  '    }',
  '',
  '    printf("\\n=== Construcao da Lista 2 ===\\n");',
  '    for (i = 0; i < MAX; i++) {',
  '        printf("Elemento %d: ", i + 1);',
  '        scanf("%d", &valor);',
  '        inserirSemRepetir(lista2, valor, &qtd2, MAX);',
  '    }',
  '',
  '    /* b) Impressao das listas */',
  '    printf("\\n=== Lista 1 ===\\n");',
  '    listar(lista1, qtd1);',
  '    printf("\\n=== Lista 2 ===\\n");',
  '    listar(lista2, qtd2);',
  '',
  '    /* c) Intercalacao */',
  '    int listaIntercalada[MAX * 2];',
  '    int qtdIntercalada = 0;',
  '    intercalar(lista1, qtd1, lista2, qtd2, listaIntercalada, &qtdIntercalada);',
  '    printf("\\n=== Lista Intercalada (c) ===\\n");',
  '    listar(listaIntercalada, qtdIntercalada);',
  '',
  '    /* d) Intersecao */',
  '    int listaIntersecao[MAX];',
  '    int qtdIntersecao = 0;',
  '    intersecao(lista1, qtd1, lista2, qtd2, listaIntersecao, &qtdIntersecao);',
  '    printf("\\n=== Intersecao das Listas (d) ===\\n");',
  '    listar(listaIntersecao, qtdIntersecao);',
  '',
  '    /* e) Impressao das listas c e d - ja feita acima */',
  '',
  '    /* f) Uniao */',
  '    int listaUniao[MAX * 2];',
  '    int qtdUniao = 0;',
  '    uniao(lista1, qtd1, lista2, qtd2, listaUniao, &qtdUniao);',
  '    printf("\\n=== Uniao das Listas (f) ===\\n");',
  '    listar(listaUniao, qtdUniao);',
  '',
  '    /* g) Remover por indice */',
  '    int indice;',
  '    printf("\\nDigite o indice (0 a %d) do elemento a remover: ", qtdUniao - 1);',
  '    scanf("%d", &indice);',
  '    if (indice < 0 || indice >= qtdUniao) {',
  '        printf("ERRO: Indice invalido!\\n");',
  '    } else {',
  '        removerPeloIndice(listaUniao, &qtdUniao, indice);',
  '        printf("\\n=== Lista Uniao apos remocao (g) ===\\n");',
  '        listar(listaUniao, qtdUniao);',
  '    }',
  '',
  '    return 0;',
  '}',
  '',
  '/* ─── Funcoes ─────────────────────────────────────────────────────── */',
  '',
  'int buscarElemento(int v[], int qtd, int valor) {',
  '    for (int i = 0; i < qtd; i++)',
  '        if (v[i] == valor) return i;',
  '    return -1;',
  '}',
  '',
  'void inserirSemRepetir(int v[], int valor, int *pos, int max) {',
  '    if (valor == 0) {',
  '        printf("ERRO: Valor zero nao e permitido!\\n");',
  '        return;',
  '    }',
  '    if (*pos >= max) {',
  '        printf("ERRO: Lista cheia (max = %d)!\\n", max);',
  '        return;',
  '    }',
  '    if (buscarElemento(v, *pos, valor) != -1) {',
  '        printf("ERRO: Valor %d ja existe na lista!\\n", valor);',
  '        return;',
  '    }',
  '    v[*pos] = valor;',
  '    (*pos)++;',
  '}',
  '',
  'void listar(int v[], int qtd) {',
  '    if (qtd == 0) { printf("Lista vazia.\\n"); return; }',
  '    printf("[ ");',
  '    for (int i = 0; i < qtd; i++) {',
  '        printf("%d", v[i]);',
  '        if (i < qtd - 1) printf(", ");',
  '    }',
  '    printf(" ]\\n");',
  '}',
  '',
  'void intercalar(int v1[], int qtd1, int v2[], int qtd2,',
  '                int resultado[], int *qtdResultado) {',
  '    int i = 0, j = 0, k = 0;',
  '    while (i < qtd1 || j < qtd2) {',
  '        if (i < qtd1) resultado[k++] = v1[i++];',
  '        if (j < qtd2) resultado[k++] = v2[j++];',
  '    }',
  '    *qtdResultado = k;',
  '}',
  '',
  'void intersecao(int v1[], int qtd1, int v2[], int qtd2,',
  '                int resultado[], int *qtdResultado) {',
  '    *qtdResultado = 0;',
  '    for (int i = 0; i < qtd1; i++)',
  '        if (buscarElemento(v2, qtd2, v1[i]) != -1)',
  '            resultado[(*qtdResultado)++] = v1[i];',
  '}',
  '',
  'void uniao(int v1[], int qtd1, int v2[], int qtd2,',
  '           int resultado[], int *qtdResultado) {',
  '    *qtdResultado = 0;',
  '    for (int i = 0; i < qtd1; i++)',
  '        resultado[(*qtdResultado)++] = v1[i];',
  '    for (int i = 0; i < qtd2; i++)',
  '        if (buscarElemento(resultado, *qtdResultado, v2[i]) == -1)',
  '            resultado[(*qtdResultado)++] = v2[i];',
  '}',
  '',
  'void removerPeloIndice(int v[], int *quantidade, int indice) {',
  '    for (int i = indice; i < *quantidade - 1; i++)',
  '        v[i] = v[i + 1];',
  '    (*quantidade)--;',
  '}',
];

const bdr = { style: BorderStyle.SINGLE, size: 1, color: 'BBBBBB' };
const borders = { top: bdr, bottom: bdr, left: bdr, right: bdr };

function traceTable(rows) {
  return new Table({
    width: { size: 9026, type: WidthType.DXA },
    columnWidths: [2800, 1500, 1500, 1500, 1726],
    rows: rows.map(cells =>
      new TableRow({
        children: cells.map((c, ci) =>
          new TableCell({
            borders,
            width: { size: [2800,1500,1500,1500,1726][ci], type: WidthType.DXA },
            shading: { fill: ci === 0 ? 'E8F0FE' : 'FFFFFF', type: ShadingType.CLEAR },
            margins: { top: 60, bottom: 60, left: 100, right: 100 },
            children: [new Paragraph({
              children: [new TextRun({ text: c, font: 'Courier New', size: 18, bold: ci === 0 })]
            })]
          })
        )
      })
    )
  });
}

const traceRows = [
  ['Momento / Evento',       'x (global)', 'numero (main)', 'outroNumero (main)', 'Obs.'],
  ['Inicio main',            '20',          '10',            '(indefinido)',       ''],
  ['Chamada Teste1(10)',      '20',          '10',            '—',                  'numero local de Teste1 = 10 (copia)'],
  ['Dentro Teste1: numero=30','20',          '10',            '—',                  'local; nao altera main'],
  ['Dentro Teste1: x++',     '21',          '10',            '—',                  ''],
  ['Retorno Teste1',         '21',          '10',            '—',                  'local numero de Teste1 liberado'],
  ['printf apos Teste1',     '21',          '10',            '—',                  '"numero=10, X=21"'],
  ['Chamada Teste2(&numero)', '21',          '10',            '—',                  'ponteiro aponta para main.numero'],
  ['Dentro Teste2: valor=100','21',          '10',            '—',                  'local valor criado'],
  ['Dentro Teste2: *numero=110','21',        '110',           '—',                  'main.numero alterado por referencia'],
  ['Dentro Teste2: x++',     '22',          '110',           '—',                  ''],
  ['Retorno Teste2',         '22',          '110',           '—',                  'ponteiro e valor locais liberados'],
  ['printf apos Teste2',     '22',          '110',           '—',                  '"numero=110, X=22"'],
  ['Chamada Teste3(110)',     '22',          '110',           '—',                  'n local = 110 (copia)'],
  ['Dentro Teste3: valor=200','22',          '110',           '—',                  'local valor criado'],
  ['Dentro Teste3: n=310',    '22',          '110',           '—',                  'local; nao altera main.numero'],
  ['Dentro Teste3: x--',     '21',          '110',           '—',                  ''],
  ['Retorno Teste3 (310)',    '21',          '110',           '310',                'n e valor locais liberados'],
  ['printf apos Teste3',     '21',          '110',           '310',                '"outroNumero=310, X=21"'],
  ['Fim main / return 0',    '21',          '—',             '—',                  'numero e outroNumero liberados'],
];

const doc = new Document({
  numbering: {
    config: [
      {
        reference: 'bullets',
        levels: [{
          level: 0, format: LevelFormat.BULLET, text: '\u2022',
          alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } }
        }]
      }
    ]
  },
  styles: {
    default: { document: { run: { font: 'Arial', size: 24 } } },
    paragraphStyles: [
      {
        id: 'Heading1', name: 'Heading 1', basedOn: 'Normal', next: 'Normal', quickFormat: true,
        run: { size: 32, bold: true, font: 'Arial', color: '1F3864' },
        paragraph: { spacing: { before: 300, after: 160 }, outlineLevel: 0,
          border: { bottom: { style: BorderStyle.SINGLE, size: 6, color: '1F3864', space: 1 } } }
      },
      {
        id: 'Heading2', name: 'Heading 2', basedOn: 'Normal', next: 'Normal', quickFormat: true,
        run: { size: 26, bold: true, font: 'Arial', color: '2E75B6' },
        paragraph: { spacing: { before: 200, after: 120 }, outlineLevel: 1 }
      },
      {
        id: 'Heading3', name: 'Heading 3', basedOn: 'Normal', next: 'Normal', quickFormat: true,
        run: { size: 24, bold: true, font: 'Arial', color: '404040' },
        paragraph: { spacing: { before: 160, after: 80 }, outlineLevel: 2 }
      },
    ]
  },
  sections: [{
    properties: {
      page: {
        size: { width: 11906, height: 16838 },
        margin: { top: 1418, right: 1418, bottom: 1418, left: 1701 }
      }
    },
    children: [
      // ── Capa / Título ───────────────────────────────────────────────
      new Paragraph({
        children: [new TextRun({ text: 'Atividade Estruturada 2', bold: true, size: 40, color: '1F3864' })],
        alignment: AlignmentType.CENTER,
        spacing: { before: 400, after: 160 }
      }),
      new Paragraph({
        children: [new TextRun({ text: 'Funções e Operações com Listas Lineares Sequenciais', size: 26, color: '2E75B6' })],
        alignment: AlignmentType.CENTER,
        spacing: { after: 80 }
      }),
      new Paragraph({
        children: [new TextRun({ text: 'Disciplina: Estrutura de Dados  |  Linguagem C', size: 22, italics: true, color: '666666' })],
        alignment: AlignmentType.CENTER,
        spacing: { after: 600 }
      }),

      // ═══════════════════════════════════════════════════════════════
      h1('Questão 1 — Análise do Programa em C'),
      spacer(),
      p([bold('Código-fonte analisado:')]),
      spacer(),
      ...codeBlock(codigoFonte),
      spacer(),

      // ── 1a ──────────────────────────────────────────────────────────
      h2('a) Variáveis Globais e Locais'),
      spacer(),
      p([bold('Variável Global:')]),
      bullet([code('int x = 20'), normal(' — declarada fora de qualquer função; acessível e modificável em todo o programa durante toda a sua execução.')]),
      spacer(),
      p([bold('Variáveis Locais (com escopo):')]),
      bullet([bold('main: '), code('numero'), normal(' (escopo: bloco main) e '), code('outroNumero'), normal(' (escopo: bloco main). Ambas existem do início da main até o '), code('return 0'), normal('.')]),
      bullet([bold('Teste1: '), code('numero'), normal(' — parâmetro formal (cópia por valor). Escopo: corpo de Teste1. Criada ao ser chamada e destruída ao retornar.')]),
      bullet([bold('Teste2: '), code('numero'), normal(' (ponteiro — parâmetro formal) e '), code('valor'), normal(' (declarada internamente). Escopo: corpo de Teste2.')]),
      bullet([bold('Teste3: '), code('n'), normal(' (parâmetro formal) e '), code('valor'), normal(' (declarada internamente). Escopo: corpo de Teste3.')]),
      spacer(),

      // ── 1b ──────────────────────────────────────────────────────────
      h2('b) Tipo de Passagem de Parâmetros'),
      spacer(),
      bullet([code('Teste1(int numero)'), normal(' — '), bold('Passagem por valor'), normal(': uma cópia do argumento é criada. Alterações no parâmetro local não afetam a variável original.')]),
      bullet([code('Teste2(int *numero)'), normal(' — '), bold('Passagem por referência (via ponteiro)'), normal(': o endereço da variável é passado, permitindo modificar diretamente o valor na memória do chamador.')]),
      bullet([code('Teste3(int n)'), normal(' — '), bold('Passagem por valor'), normal(': cópia do argumento; alterações em '), code('n'), normal(' não refletem em '), code('main'), normal('.')]),
      spacer(),

      // ── 1c ──────────────────────────────────────────────────────────
      h2('c) Rastreamento Passo a Passo das Variáveis'),
      spacer(),
      p([normal('A tabela abaixo mostra o estado de cada variável a cada evento. Quando uma variável é liberada da memória, isso é indicado na coluna "Obs.".')]),
      spacer(),
      traceTable(traceRows),
      spacer(),

      // ── 1d ──────────────────────────────────────────────────────────
      h2('d) Saída Impressa na Tela'),
      spacer(),
      p([normal('A execução do programa imprime exatamente o seguinte:')]),
      spacer(),
      ...codeBlock([
        'Valor de numero (apos Teste1) = 10',
        'X = 21',
        'Valor de numero (apos Teste2) = 110',
        'X = 22',
        'Valor de outro numero (apos Teste3) = 310',
        'X = 21',
      ]),
      spacer(),
      p([bold('Justificativa resumida:')]),
      bullet([
        bold('Após Teste1: '), normal('numero permanece 10 porque a passagem foi por valor (cópia). '),
        code('x'), normal(' foi incrementado de 20 para 21.')
      ]),
      bullet([
        bold('Após Teste2: '), normal('numero passa a 110 (10 + 100) pois foi passado por referência. '),
        code('x'), normal(' foi incrementado de 21 para 22.')
      ]),
      bullet([
        bold('Após Teste3: '), normal('outroNumero recebe o retorno 310 (110 + 200). '),
        code('x'), normal(' foi decrementado de 22 para 21.')
      ]),
      spacer(),

      // ═══════════════════════════════════════════════════════════════
      h1('Questão 2 — Programa: Listas Lineares Sequenciais'),
      spacer(),
      p([normal('O programa implementa todas as operações solicitadas sobre listas lineares sequenciais não ordenadas de inteiros não nulos, conforme os itens a–g.')]),
      spacer(),

      h2('Visão Geral das Funções Implementadas'),
      bullet([code('inserirSemRepetir'), normal(' — insere elemento verificando: lista cheia, valor nulo e duplicidade (busca sequencial).')]),
      bullet([code('listar'), normal(' — percorre e imprime todos os elementos da lista.')]),
      bullet([code('intercalar'), normal(' — gera nova lista alternando elementos das duas listas de entrada.')]),
      bullet([code('intersecao'), normal(' — gera lista com elementos presentes nas duas listas (interseção de conjuntos).')]),
      bullet([code('uniao'), normal(' — gera lista com todos os elementos distintos das duas listas.')]),
      bullet([code('removerPeloIndice'), normal(' — remove elemento pelo índice, deslocando os posteriores uma posição à esquerda.')]),
      bullet([code('buscarElemento'), normal(' — busca sequencial auxiliar; retorna o índice ou -1.')]),
      spacer(),

      h2('Código-Fonte Completo'),
      spacer(),
      ...codeBlock(q2codigo),
      spacer(),

      // ── Explicação por item ──────────────────────────────────────────
      h2('Explicação por Item'),

      h3('a) inserirSemRepetir'),
      p([normal('A função realiza três verificações antes de inserir:')]),
      bullet([bold('Valor zero: '), normal('inteiros não nulos são exigidos; zero é rejeitado com mensagem de erro.')]),
      bullet([bold('Lista cheia: '), normal('se '), code('*pos >= max'), normal(', a inserção é impedida.')]),
      bullet([bold('Duplicidade: '), normal('a função auxiliar '), code('buscarElemento'), normal(' percorre o vetor sequencialmente; se encontrar o valor, a inserção é ignorada com aviso.')]),
      bullet([bold('Inserção: '), normal('se todas as verificações passarem, o elemento é colocado em '), code('v[*pos]'), normal(' e '), code('*pos'), normal(' é incrementado via ponteiro.')]),
      spacer(),

      h3('b) listar / percorrer'),
      p([normal('Percorre o vetor de 0 até '), code('qtd - 1'), normal(' imprimindo cada elemento. Caso a lista esteja vazia (qtd = 0), exibe "Lista vazia."')]),
      spacer(),

      h3('c) Intercalação'),
      p([normal('Usa dois índices ('), code('i'), normal(' e '), code('j'), normal(') que percorrem as listas simultaneamente. Em cada iteração, insere primeiro um elemento de v1 (se disponível) e depois um de v2 (se disponível). Exemplo: L1 = {10, 34, 5}, L2 = {4, 7, 9} → Intercalada = {10, 4, 34, 7, 5, 9}.')]),
      spacer(),

      h3('d) Interseção'),
      p([normal('Para cada elemento de L1, verifica se ele também existe em L2 usando '), code('buscarElemento'), normal('. Se sim, é incluído na lista resultado. Garante apenas elementos comuns às duas listas.')]),
      spacer(),

      h3('e) Impressão das listas c e d'),
      p([normal('As listas intercalada e de interseção são impressas com a mesma função '), code('listar'), normal(', reutilizando o código de percurso já implementado.')]),
      spacer(),

      h3('f) União'),
      p([normal('Primeiro copia todos os elementos de L1 para o resultado. Depois percorre L2: cada elemento só é adicionado se ainda não estiver no resultado (verificado por '), code('buscarElemento'), normal('). Isso garante a propriedade de conjunto (sem duplicatas).')]),
      spacer(),

      h3('g) removerPeloIndice'),
      p([normal('A validação do índice é feita na '), code('main'), normal(' antes de chamar a função. Dentro da função, os elementos posteriores ao índice são deslocados uma posição à esquerda ('), code('v[i] = v[i+1]'), normal('), e '), code('*quantidade'), normal(' é decrementado via ponteiro para refletir o novo tamanho.')]),
      spacer(),

      h2('Exemplo de Execução'),
      spacer(),
      p([normal('Entrada: Lista 1 = {10, 34, 5, 8, 2}, Lista 2 = {4, 7, 9, 34, 8}')]),
      spacer(),
      ...codeBlock([
        '=== Lista 1 ===',
        '[ 10, 34, 5, 8, 2 ]',
        '',
        '=== Lista 2 ===',
        '[ 4, 7, 9, 34, 8 ]',
        '',
        '=== Lista Intercalada (c) ===',
        '[ 10, 4, 34, 7, 5, 9, 8, 34, 2, 8 ]',
        '',
        '=== Intersecao das Listas (d) ===',
        '[ 34, 8 ]',
        '',
        '=== Uniao das Listas (f) ===',
        '[ 10, 34, 5, 8, 2, 4, 7, 9 ]',
        '',
        'Digite o indice (0 a 7) do elemento a remover: 2',
        '',
        '=== Lista Uniao apos remocao (g) ===',
        '[ 10, 34, 8, 2, 4, 7, 9 ]',
      ]),
      spacer(),

    ]
  }]
});

Packer.toBuffer(doc).then(buf => {
  fs.writeFileSync('/mnt/user-data/outputs/Atividade_Estruturada_2.docx', buf);
  console.log('OK');
});
