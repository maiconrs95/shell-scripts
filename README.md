# Introdução - O que são Shell Scripts?

São scripts interpretados por algum programa [Shell](https://pt.wikipedia.org/wiki/Shell_script), sendo Shell uma interface de linha de comando (CLI) que permite o usuário acessar serviços do sistema operacional.

Nos exemplos desse artigo vamos utilizar o [bash](https://pt.wikipedia.org/wiki/Bash) como interpretador.

O Bash está disponível por padrão nos sistemas operacionais Unix-like como Linux e macOS.

> É recomendável que você tenha algum conhecimento básico do uso da CLI antes de começar a escrever shell scripts.

#### Índice

- [Criando o primeiro script](#criando-o-primeiro-script)
- [Strings](#strings)
- [Variáveis](#variáveis)
- [Matrizes](#matrizes)
- [Execução shell](#execução-shell)
- [Entradas do usuário](#entradas-do-usuário)
- [Operadores de comparação](#operador-de-comparação)
- [Condições](#condições)
- [Loops](#loops)
- [Funções](#funções)
- [Conclusão](#conclusão)
- [Agradecimentos](#agradecimento)

## Criando o primeiro script

Crie o arquivo `hello-world`:

```shell
# touch é o comando usado para criar um arquivo
touch hello-world
```

Dentro do arquivo vamos imprimir um texto usando o comando `echo`:

```shell
echo "Hello, world!"
```

Agora, usando a linha de comando, podemos executar nosso script com o interpretador `bash`:

```shell
bash hello-world

# Output: Hello, world!
```

Pronto, temos nosso primeiro shell script.

### Scripts executáveis

Aprendemos a executar um script prefixado de bash, que é o responsável por interpretar os comandos dentro do nosso arquivo, mas se tentarmos simplesmente executar o arquivos pelo nome, ele não será executado.

>  `./` é usado para indicar que arquivo está no diretório atual

```shell
./hello-world

# Output: bash: ./hello-world: Permissão negada
```

Para executar um arquivo diretamente, precisaremos alterar as permissões e permitir que o script seja executado pelo usuário. `chmod` é um comando que altera as permissões em um arquivo e `+x` adiciona direitos de execução ao script.

```shell
chmod +x hello-world
```

Também é necessário adicionar o [hashbang (#!)](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) na parte superior do script. No Unix, arquivos com o `hashbang` é interpretado como um executável.

Para confirmar onde o intérprete está localizado:

```shell
which bash

# Output: /usr/bin/bash
```

Então adicionamos `#!/bin/bash` no início do script:

```shell
#!/bin/bash

echo "Hello, world!"
```

> Você também pode ver `#!/usr/bin/env bash`, que pode ser usado se você não souber o caminho exato para o bash.

Feito isso, `hello-world` pode ser executado diretamente:

```shell
./hello-world

# Output: Hello, world!
```

> Para executar um script bash sem especificar o diretório (usando `./`, por exemplo), você deverá adicionar o diretório do script ao PATH executando `export PATH=$PATH:/path/to/script/directory`. No entanto, isso geralmente não é necessário para scripts pessoais.

## Strings

Uma sequência de caracteres simples que no bash não requer aspas, como normalmente vimos em linguagens de programação:

```shell
echo My String

# Output: My String
```

Para usar aspas em strings é necessário o uso do [escape](https://pt.wikipedia.org/wiki/Caractere_de_escape) `\`:

```shell
echo I\'m a string

# Output: I'm a string
```

Também podemos envolver a string em aspas duplas ou simples para omitir o uso de `\`:

```shell
echo "I'm a string" # I'm a string
echo 'A simple "string" here' # A simple "string" here
```

Com a flag `-e`, o bash iterpretará strings com caracteres de escape, como por exemplo o `\n` para pular uma linha. Isso requer a string entre aspas:

```shell
echo -e "This string has a \nnew line"

# Output:
# This string has a
# new line
```

As strings entre aspas duplas também são importantes para uso com variáveis, como veremos na próxima seção.

## Variáveis

Podemos usar variáveis ​​como em qualquer linguagem de programação. Não há tipos de dados. Uma variável no bash pode conter um número, um caractere ou uma sequência de caracteres.

Também não é necessário declarar a variável, apenas atribuir um valor irá cria-la:

```shell
STR="Hello, My First Var!"

echo $STR

# Output: Hello, My First Var!
```

> Nota: Observe que não deve haver espaços entre o sinal de igual e o nome e o valor da variável.

Cadeias de caracteres entre aspas duplas são necessárias para interpolar variáveis. Entre aspas simples, o cifrão seria interpretado literalmente:

```shell
WHO="Maicon"

echo 'Hello, $WHO'   # Hello, $WHO
echo "Hello, $WHO"   # Hello, Maicon
echo "Hello, ${WHO}" # Hello, Maicon
```

Essa sintaxe é necessária para qualquer coisa mais complexa que  possamos fazer com uma variável, como obter um item de uma matriz, cálculos, condicionais, escopos etc.

## Matrizes

Uma matriz no bash é definida entre parênteses e não há virgulas entre seus itens:

```shell
games=('TLOU 2', 'God of War', 'Gost of Tushima')
```

E para acessarmos dados de uma matriz, usa-se colchetes `[]`. As matrizes são indexadas em `0`:

```shell
echo ${games[0]} # TLOU 2
echo ${games[2]} # Gost of Tushima
```

> Nota: para imprimir ou recuperar dados de matrizes, também é necessário o uso de `{}`.

## Execução shell

Podemos atribuir a saída de um comando em tempo de execução adicionando `$()` em torno do comando. Ex, para atribuir a data atual no formato `dd-mm-yy hh:mm` em um uma variável no nosso script, ficaria `VAR=$(date +"%d-%m-%y %H:%M")`:

```shell
DATE_NOW=$(date +"%d-%m-%y %H:%M")

echo $DATE_NOW

# Output: 25-06-20 16:50
```

Observer que o valor de `DATE_NOW` é a saída do nosso comando em `$(COMANDO)`.

## Entrada do usuário

Já vimos como atribuir valores a uma variável via script, mas também podemos solicitar ao usuário esse valor via CLI. Isso é feito pelo comando `read`:

```shell
echo 'Who are you?'

read who

echo "Hello, $who!"

# Output: Who are you?
# > Maicon
# Hello, maicon!
```

## Operadores de comparação

Os operadores de comparação em scripts bash pode ser um pouco diferente se comparados a linguagens de programação como o JavaScript.

Por exemplo, é diferente quando comparamos números e quando comparamos cadeias de caracteres.

Aqui vai uma tabela com alguns exemplos:

| Números | Strings | Descrição |
| ------------- |:-------------:| -----:|
| `-eq`   | `==`    | Igual             |
| `-ne`   | `!=`    | Não igual         |
| `-gt`   | `>`     | Maior que         |
| `-ge`   | `>=`    | Maior ou igual    |
| `-lt`   | `<`     | Menor             |
| `-le`   | `<=`    | Menor ou igual    |

Támbém é possível usar `-z` para testar o vazio em uma sequência de caracteres.

## Condições - Comandos de seleção ou de tomada de decisão

Em diversos momentos, um script precisa tomar uma ação baseado em alguma condição, um `if else`.

A estrutura condicional de um bash é `if`, `test ou [ CONDICAO ]`, `then`, `else`, e `fi` para finalizar o bloco.

Em `CONDICAO`, é possível utilizar os operadores que vimos na tabela da seção anterior:

```shell
if [ CONDICAO ];
then
  AÇÕES
fi
```

- `CONDICAO`: Avalia a condição passada, se verdadeiro executa a instrução do bloco `then`;
- `ELSE`: É executado quando [ CONDICAO ] não for verdadeira;
- `AÇÕES`: Comandos que vão executar dentro do bloco;

> Nota: Para fechar um if é necessário `fi` no final do bloco

> Os colchetes '[ CONDICAO ]' são um atalho para o comando test. também poderia ser escrito "if test CONDICAO"

Vamos ao exemplo, um script que mostra se a idade passada no console é de uma pessoa maior ou menor de idade:

```shell
#!/bin/bash

echo "Qual sua idade?"

read age

if [ $age -gt 17 ]
then
    echo 'Maior de idade'
else
    echo 'Menor de idade'
fi
```

### Elif - else if?

Em alguns casos é necessário testar mais de uma condição, todas correlacionadas. Para isso temos o `elif`:

```shell
if [ CONDICAO_1 ];
then
    AÇÕES_1
elif [ CONDICAO_2 ];
then
    AÇÕES_2
elif [ CONDICAO_3 ];
then
    AÇÕES_3
    .
    .
    .
elif [ CONDICAO_N ];
then
    AÇÕES_N
fi
```
Para ficar mais claro, vamos usar em um exemplo prático.

Vamos apresentar um menu para o usuário escolher uma opção. Baseado nesta escolha, a hora e a data serão exibidas; uma divisão será efetuada e seu resultado será exibido, e uma mensagem será exibida com o nome que o usuário fornecer:

```shell
#!/bin/bash

echo "Selecione uma opção:"
echo "1 - Exibir data e hora do sistema"
echo "2 - Exibir o resultado da divisão 10/2"
echo "3 - Exibir uma mensagem"

read opcao;

if [ $opcao -eq 1 ]
then
    data=$(date +"%d-%m-%y %H:%M")
    echo "$data"
elif [ $opcao -eq 2 ]
then
    result=$((10/2))
    echo "divisao de 10/2 = $result"
elif [ $opcao -eq 3 ]
then
    echo "Qual seu nome?"
    read name
    echo "Bem-vindo ao shell script, $name"
fi
```

### Case

O comando `case` tem a mesma funcionalidade do `if...then...elif`, com a diferença de sua sintaxe ser mais compacta e enxuta:

```shell
case VARIAVEL in
    CASO_1)
        AÇÕES_1
    ;;
    CASO_2)
        AÇÕES_2
    ;;
    CASO_N)
        AÇÕES_N
    ;;
esac
```

Para efeitos de comparação, vamos modificar o script visto em `elif` para usar o `case`:

```shell
#!/bin/bash

echo "Selecione uma opção:"
echo "1 - Exibir data e hora do sistema"
echo "2 - Exibir o resultado da divisão 10/2"
echo "3 - Exibir uma mensagem"

read opcao

case $opcao in
    1)
        data=$(date +"%d-%m-%y %H:%M")
        echo "$data"
    ;;
    2)
        result=$((10/2))
        echo "divisao de 10/2 = $result"
    ;;
    3)
        echo "Qual seu nome?"
        read name
        echo "Bem-vindo ao shell script, $name"
    ;;
esac
```

## Loops

O uso de loops é muito comum quando precisamos iterar sobre um determinado valor até alguma condição seja satisfeita ou que o laço chegue ao fim.

O Bash usa os loops `for`, `while` e `until`.

```shell
for VARIAVEL in VALOR_1, VALOR_2 … VALOR_N;
    do
        AÇÕES
    done
```

- `VARIAVEL`: Variável cujo valor será inicializado e incrementado, respeitando os limites dos valores do conjunto fornecido;
- `VALOR_1, VALOR_2, VALOR_N`: valores que `VARIAVEL` poderá assumir durante o `loop`;
- `AÇÕES`: Ações a serem tomadas dentro do `loop`.

> Nota: A sequência `VALOR_1, VALOR_2, VALOR_N` pode ser substituida por `{VALOR_1..VALOR_N}`.

Por exemplo, podemos utilizar o `for..in` para listar todas as pastas em algum diretório do sistema:

```shell
echo -e "Testando a listagem de pastas"

files=~/folder/subfolder/*

for file in $files
do
  echo $(basename $file)
done
```

A saída do script é o nome de cada pasta presente no diretório informado em `files`.

### While

Um outro exemplo bem comum é o uso do `while..do`, que executa uma ação até que a condição de saída seja atendida:

```shell
echo "Informe o que você quiser, -1 para sair"

read dado;

while [ $dado != "-1" ];
    do
        echo “Você digitou $dado”
    read dado;
done
```

O bloco `do` será executado enquanto o valor imputado pelo usuário for diferente de `-1`.

## Funções

Funções são usadas para organizar estruturas lógicas, e no shell script não é diferente.

Para criar uma função é bem simples, basta:

```shell
nome_funcao() {
    AÇÕES
}
```

Um exemplo bem básico onde o usuário pode optar por qual função chamar:

```shell
#!/bin/bash

main() {
    echo "Escolha uma opção:"
    echo "1 - Printar nome"
    echo "2 - Printar idade"

    read opcao

    case $opcao in
    "1")
        print_name
    ;;
    "2")
        print_age
    ;;
    esac
}

print_name() {
    echo "Qual seu nome?"

    read name

    echo "Seu nome é $name"
}

print_age() {
    echo "Qual sua idade?"

    read age

    echo "Sua idade é $age"
}

main
```

> Nota: Lembre-se sempre de chamar a função principal (no nosso caso, main) no final do script, do contrário, nada acontecerá quando o script for executado.

### Argumentos

Argumentos são muito comuns como dados de entrada em um programa.

O shell script tem alguns nomes especiais para argumentos recebidos por um script:

- `$0`: Contém o nome do script que foi executado;
- `$1 --- $n`: Contém os argumentos na ordem em que foram passados (1º argumento em $1, 2º argumento em $2, etc.).
- `$#`: Contém o número de argumentos que foi passado (ou seja, não considera o nome do script em $0);
- `$*`: Retorna todos os argumentos de uma única vez.

Por exemplo:

```shell
#!/bin/bash

if [ $# -lt 1 ];
then
  echo "Precisa fornecer pelo menos 1 argumento!"
  exit 1
fi

echo "Número de argumentos passados: $#"

i=0

for argumento in $*
  do
    i=$(($i+1))
    echo "Argumento $i passado: $argumento"
done
```

O script acima pede que pelo menos um argumento seja passado:

```shell
bash arguments Maicon

# Output:
# Número de argumentos passados: 1
# Argumento 1 passado: Maicon
```

## Conclusão

Nesse artigo vimos o necessário para criar e executar um shell script. Com isso já é possível começar a criar scripts mais avançados para automatizar tarefas, rotinas etc.

Com shell scripts podemos instalar programas, realizar backups, executar tarefas etc. Aqui tem uma infinidade de possibilidades.

Um caso de uso bem legal é criar um script para preparar seu ambiente, e não ter que ficar instalando e configurando a máquina sempre que precisar formatar.

## Agradecimentos

- https://www.taniarascia.com/how-to-create-and-use-bash-scripts/
- https://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO-5.html
- https://en.wikipedia.org/wiki/Shebang_%28Unix%29
- https://www.devmedia.com.br/introducao-ao-shell-script-no-linux/25778
- https://pt.wikipedia.org/wiki/Bash
