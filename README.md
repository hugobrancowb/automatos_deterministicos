# Programa AUTOMAX 
Avaliação para a disciplina de Matemática Discreta na Universidade de Pernambuco em 2018.1 sob a orientação do professor Ruben Carlo Benante. O objetivo é realizar conversões entre autômatos finitos determinísticos e não determinísticos e expressões regulares.

## Converte AFND->AFD->ER->AFND

### Entrada de Dados ###

O programa aceita dois tipos de entrada:

1- Uma quíntupla que pode representar um AFD ou um AFND
2- Uma _string_ que representa uma expressão regular

As entradas podem vir de duas fontes:

1- Leitura de arquivo (_file_)
2- Leitura da entrada padrão (_stdin_)

#### Opções do sistema (flags)

* -n : a entrada de dados é uma quíntupla de um AFND que será convertido para AFD.
* -d : a entrada de dados é uma quíntupla de um AFD que será convertido para uma ER.
* -e : a entrada de dados é uma _string_ de uma ER que será convertida em um AFND.
* -f _file_ : faz a entrada de um arquivo.
* -h : mostra o help e termina.
* -v : aumenta a _verbosidade_ (cumulativo).
* -V : mostra a versão e o copyright e termina.

A flag `-f file` é opcional. Se existir a entrada é do arquivo indicado na linha de comando. Se não existir esta flag, o programa deverá ler da entrada padrão (_stdin_).

É obrigatório ter uma e somente uma das flags `-n`, `-d` ou `-e`, que indicará o tipo de conversão. Caso não exista nenhuma, imprima um _help_ e termine. A flag de entrada pode ser seguida ou não da flag opcional de arquivo.

##### Formato da quíntupla (AFD e AFND)

A entrada lê uma quíntupla que descreve formalmente um autômato (determinístico ou não-determinístico).

- K: Conjunto de estados
    - Tipo: unsigned short K;
    - Constrição: 1 <= K <= SHORT\_MAX
    - Exemplo: K=5, então os estados são {q0, ..., q4}
    - Se SHORT\_MAX = 65535, então o maior estado é q65534.
    - Ficam reservados os valores negativos para indicar erro.
- A: Alfabeto
    - Tipo: char A;
    - Constrição: Apenas letras minúsculas de 'a' a 'z'.
    - Exemplo: A='c', então o alfabeto é {a, b, c}
- S: Estado inicial (único)
    - Tipo: short S;
    - Constrição: 0 <= S < K
    - Exemplo: S=0, então o estado inicial é q0.
    - Reserva-se valores negativos para usar como código de erro.
- F: Conjunto de estados finais
    - Tipo: lista encadeada simples.
    - Conteúdo: números inteiros short referentes a todos estados que são finais.
    - Reservados valores negativos para indicar erros.
- D: Função Delta, a função de transição do autômato
    - Tipo: lista encadeada simples.
    - Conteúdo: mapeamento (estado inicial, símbolo lido, estado final) para cada transição do autômato.
    - A função d(ei, le, ef), se para todo d(...) é determinado univocamente o estado final a partir do estado inicial e do símbolo lido, e os símbolos lidos não são VAZIO (`ξ`, epsilon) para todo d(...), então a quíntupla é um AFD. Caso contrário trata-se de um AFDN.
    - Ficam reservados valores negativos e NULL para indicar erros ou outras condições de problema.

###### Exemplo de tabela da função de transição de um AFND:

| Ei | Le | Ef |
|----|----|----|
| q0 |  a | q1 |
| q0 |  b | q1 |
| q0 |  ξ | q2 |
| q1 |  a | q1 |
| q1 |  ξ | q0 |
| q2 |  a | q0 |
| q2 |  ξ | q1 |

##### Formato da string da Expressão Regular

A string é formada pelos seguintes caracteres e somente estes:

- a-z : símbolos do alfabeto (letras minúsculas)
- ξ   : símbolo vazio (epsilon, letra E maiúscula)
- |   : disjunção (OU)
- .   : conjunção (E) (explícitar obrigatoriamente. Não aceite _strings_ com conjunção implícita, exemplo: _ab_. Deve ser _a.b_)
- *   : estrela de Kleene (repete o símbolo anterior zero ou mais vezes)
- ()  : parênteses (muda a prioridade de execução das operações. Exemplo: `a|b.a ≠ (a|b).a`, ou então `a.b*.b ≠ (a.b)*.b`

A entrada será de uma única linha com uma _string_ (não são aceitas _strings_ em múltiplas linhas).

### Processamento

- Determinar pelas flags o tipo de conversão a ser realizado
- Receber a entrada como determinado pelas flags (arquivo ou entrada padrão)
- Imprimir a resposta no mesmo formato explicado acima, na saída padrão (não usar arquivos)

### Saída

**Consequência da padronização dos dados de entrada e saída:**

Uma vez que os dados lidos e de saída tem a mesma padronização, é possível chamar o programa em cadeia de _pipes_ para que a saída de um seja a entrada de outro. Por exemplo, dada uma entrada de uma quíntupla representando um AFND, podemos executar:

`$cat entradaAFND.txt | ./ex10.x -n | .ex10.x -d`

E teremos a conversão de um `AFND -> AFD -> ER`. Se continuarmos, voltaremos ao `AFND`:

`$cat entradaAFND.txt | ./ex10.x -n | ./ex10.x -d | ./ex10.x -e`