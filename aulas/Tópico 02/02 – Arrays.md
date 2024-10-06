# Tópico 02 – Strings e Arrays [<img src="images/colag_logo.svg" style="float: right; vertical-align: middle; width: 42px; height: 42px;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2002/02%20%E2%80%93%20Arrays.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 12px; vertical-align: middle; width: 36px; height: 36px;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2002/02%20%E2%80%93%20Arrays.ipynb)

Os `arrays` representam um conjunto básico de dados. Vamos aprender como usá-los!

### Resultados Esperados
1. Entender mais sobre os tipos de variáveis em Python, em particular sobre o tipo `str` (string).
1. Aprender um pouco sobre a biblioteca [NumPy](https://numpy.org/).
1. Aprender o básico sobre a manipulação dos `array`s.

### Referências
- [BPD, Capítulos 7 a 8](https://notes.dsc10.com/)
- [CIT, Capítulo 5](https://inferentialthinking.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)

## Strings

### O que é uma "string"?

- Uma string é um trecho de texto de qualquer comprimento.
- Em Python, as strings são declaradas como valores entre aspas simples ou duplas, e sua saída/output é dada apenas com aspas simples.


```python
'woof'
```




    'woof'




```python
type('woof')
```




    str




```python
"woof"
```




    'woof'




```python
# Uma string, não um int!
"1998"
```




    '1998'



### Aritmética de strings

Ao usar o símbolo `+` entre duas strings, a operação correspondente é chamada de "concatenação".


```python
s1 = 'baby'
s2 = '🐼'
```


```python
s1 + s2
```




    'baby🐼'




```python
s1 + ' ' + s2
```




    'baby 🐼'



Como uma multiplicação por um números inteiro $k$ pode ser entendida como uma soma de "$k$ vezes" o objeto multiplicado, a multiplicação de `str` por `int` é bem definida, e também é uma concatenação.  


```python
s2 * 3
```




    '🐼🐼🐼'



Porém, o mesmo não ocorre para uma multiplicação de `str` por um `float`!


```python
# Descomente e execute
# s2 * 3.2
```

### Métodos de string
- As strings estão associadas a certas funções chamadas **métodos de string**.
- Acessamos os métodos de string com um `.` após a string correspondente ("notação de ponto").
- Por exemplo, para usar o método `upper` na string `s`, escrevemos `s.upper()`.
- Exemplos comuns são `upper`, `title` e `replace`.


```python
my_cool_string = 'data science is super cool!'
```


```python
my_cool_string.title()
```




    'Data Science Is Super Cool!'




```python
my_cool_string.upper()
```




    'DATA SCIENCE IS SUPER COOL!'




```python
my_cool_string.replace('super cool', '💯' * 3)
```




    'data science is 💯💯💯!'




```python
# Apesar da função `len` operar sobre uma string, note que ela *não é um método*, pois aqui não utilizamos a notação de ponto.
len(my_cool_string)
```




    27



### Caracteres especiais em strings

Aspas simples e aspas duplas geralmente são intercambiáveis em uma string, exceto quando a _própria string_ contém aspas simples ou duplas.


```python
# Descomente e execute
# 'my string's full of apostrophes!'
```


```python
"my string's full of apostrophes!"
```




    "my string's full of apostrophes!"




```python
# Podemos evitar o erro acima com uma barra invertida/backslash!
'my string\'s "full" of apostrophes!'
```




    'my string\'s "full" of apostrophes!'




```python
print('my string\'s "full" of apostrophes!')
```

    my string's "full" of apostrophes!
    

### Além: `print`

Como padrão, os notebooks Jupyter exibem o valor "bruto" da expressão da _última linha_ de uma célula.


```python
# Aqui o número 12 não vai aparecer na saída
12
23
```




    23



A função `print`, por outro lado, tem como saída o valor em texto legível de uma string, e permite _mais de uma saída por célula_.


```python
# Note que aqui não teremos o número da execução da célula à esquerda! Esse número somente aparece sem o uso da função print
print(12)
print(23)
```

    12
    23
    


```python
# Observe a diferença do resultado da célula acima
print(12)
23
```

    12
    




    23




```python
# O caractere '\n' insere uma nova linha em uma string
my_newline_str = 'here is a string with two lines.\nhere is the second line'  
my_newline_str
```




    'here is a string with two lines.\nhere is the second line'




```python
# Porém, o output aparece corretamente apenas invocando a função print!
print(my_newline_str)  
```

    here is a string with two lines.
    here is the second line
    

### Conversão de outros tipos para strings
- Qualquer valor pode ser convertido em uma string usando ```str```.
- Por outro lado, apenas algumas strings podem ser convertidas para ```int``` e ```float```.


```python
str(3)
```




    '3'




```python
float('3')
```




    3.0




```python
int('4')
```




    4




```python
# Descomente e execute
# int('baby panda')
```

### Exercício ✅

Suponha que você tenha executado as seguintes instruções:

```py
x = 3
y = '4'
z = '5.6'
```

Escolha abaixo a expressão que será avaliada **sem** erro.

A. `x + y`

B. `x + int(y + z)`

C.`str(x) + int(y)`

D. `str(x) + z`

E. Todos eles têm erros

## Listas

### Motivação

Como armazenaríamos as temperaturas de cada um dos primeiros 6 dias do mês de setembro?

Nossa melhor solução até agora é simplesmente criarmos uma variável separada para cada dia:


```python
temperature_on_sept_01 = 84
temperature_on_sept_02 = 78
temperature_on_sept_03 = 81
temperature_on_sept_04 = 75
temperature_on_sept_05 = 79
temperature_on_sept_06 = 75
```

Isso _tecnicamente_ nos permite fazer coisas como calcular a temperatura média durante os primeiros 6 dias:


```python
avg_temperature = 1/6 * (
    temperature_on_sept_01
    + temperature_on_sept_02
    + temperature_on_sept_03
    + temperature_on_sept_04
    + temperature_on_sept_05
    + temperature_on_sept_06)
avg_temperature
```




    78.66666666666666



Apesar de funcionar, essa abordagem é altamente ineficiente!

Imagine se tivéssemos que lidar com os dados de um mês ou ano inteiros; precisaríamos de uma solução melhor.

### Listas em Python

Em Python, uma lista é usada para armazenar vários valores em um único valor/variável. 

Para criar uma nova lista do zero, usamos colchetes `[ ]`.


```python
temperature_list = [84, 78, 81, 75, 79, 75]
```


```python
len(temperature_list)
```




    6



Note que os elementos de uma lista, diferente das variáveis que declaramos, não precisam ser únicos!

Voltando ao nosso problema inicial, para encontrar a temperatura média, basta dividirmos a **soma das temperaturas** pelo **número de temperaturas registradas**:


```python
temperature_list
```




    [84, 78, 81, 75, 79, 75]




```python
sum(temperature_list) / len(temperature_list)
```




    78.66666666666667



### Tipos de listas

O `tipo` de uma lista é... `lista`.


```python
temperature_list
```




    [84, 78, 81, 75, 79, 75]




```python
type(temperature_list)
```




    list



Dentro de uma lista, você pode armazenar elementos de diferentes tipos:


```python
mixed_list = [-2, 2.5, 'ucsd', [1, 3]]
mixed_list
```




    [-2, 2.5, 'ucsd', [1, 3]]



### Porém...

- As listas são **muito lentas**.
- Isso não é grande coisa quando não há muitas entradas, mas é um grande problema quando há milhões ou bilhões de entradas, situação que frequentemente encontramos em Ciência de Dados.

## Arrays

### NumPy

<center>
<img src='images/numpy.png' width=400>
</center>

- NumPy (pronuncia-se "num pie") é uma biblioteca/módulo do Python que fornece suporte para **arrays** e operações nos mesmos.

- A biblioteca `babypandas`, sobre a qual você aprenderá no próximo tópico, anda de mãos dadas com o NumPy.
  
- NumPy é muito usado no mundo real!

Geralmente importamos o `numpy` como `np`, mas note que esse é apenas um _alias_.


```python
import numpy as np
```

Os "pseudônimos"/aliases são utilizados apenas para facilitar a leitura do código, e portanto não são estritamente necessários.

### Arrays

Pense nos arrays do NumPy (referidos de agora em diante apenas como "arrays") como listas sofisticadas e mais rápidas.

<center><img src="images/squid.png" width=30%></center>

Para criar um array, passamos uma lista como argumento para a função `np.array`.


```python
np.array([4, 9, 1, 2])
```




    array([4, 9, 1, 2])




```python
# Note mais uma vez que o alias "np" não é necessário; o código abaixo é equivalente ao acima
# import numpy
# numpy.array([4, 9, 1, 2])
```


```python
temperature_array = np.array([84, 78, 81, 75, 79, 75])
temperature_array
```




    array([84, 78, 81, 75, 79, 75])




```python
temperature_list
```




    [84, 78, 81, 75, 79, 75]




```python
# Aqui não precisamos de colchetes [], porque o argumento `temperature_list` já é uma lista!
np.array(temperature_list)
```




    array([84, 78, 81, 75, 79, 75])



### Posições/indexação

Quando as pessoas ficam em fila, cada pessoa tem uma posição específica.

<center><img src="images/position.png" width=50%></center>

Da mesma forma, cada elemento de um array (ou lista) também possui uma posição única.

### Acessando um elemento de um array com base em sua posição

- O Python, como algumas linguagens de programação, é "indexado em 0".
- Isso significa que a posição do primeiro elemento em uma matriz é 0, não 1!
- Um motivo para isso é que podemos pensar na posição de um elemento como "o número de elementos à sua frente".
- Para acessar o elemento do array `arr_name` na posição `pos`, usamos a sintaxe `arr_name[pos]`.
- É comum nos referirmos à posição `pos` como o _índice_ do array `arr_name` correspondente ao elemento `arr_name[pos]`.


```python
temperature_array
```




    array([84, 78, 81, 75, 79, 75])




```python
temperature_array[0]
```




    84




```python
temperature_array[1]
```




    78




```python
temperature_array[3]
```




    75




```python
# Último elemento do array
temperature_array[5]
```




    75




```python
# Descomente e execute
# temperature_array[6]
```


```python
# Se o índice é negativo, contamos do final para o início!
temperature_array[-1]
```




    75




```python
temperature_array[-6]
```




    84




```python
# Descomente e execute
# temperature_array[-7]
```

### Matrizes e vetores

No NumPy, as matrizes e os vetores são nada mais que arrays bidimensionais e unidimensionais, respectivamente.


```python
vX = np.array([1, 2, 3]) # esse é um vetor!
vX
```




    array([1, 2, 3])




```python
vX[1]
```




    2




```python
mX = np.array([[1, 2, 3], [4, 5, 6]]) # essa é uma matriz!
mX
```




    array([[1, 2, 3],
           [4, 5, 6]])




```python
mX[0, 2]
```




    3



### Tipos de variáveis em um array

Vimos acima que as listas podem armazenar elementos de vários tipos.


```python
nums_and_strings_lst = ['uc', 'sd', 1961, 3.14]
nums_and_strings_lst
```




    ['uc', 'sd', 1961, 3.14]



Isso, porém, não é verdade para os arrays: todos os elementos de um array devem ser **do mesmo tipo**.


```python
# Aqui, todos os elementos da lista são convertidos para str!
np.array(nums_and_strings_lst)
```




    array(['uc', 'sd', '1961', '3.14'], dtype='<U32')



### Aritmética com os valores de um array

Os arrays facilitam a execução da mesma operação em todos os elementos.

Este comportamento é formalmente conhecido como "transmissão", ou "vetorização".


```python
temperature_array
```




    array([84, 78, 81, 75, 79, 75])




```python
# Aumentar todas as temperaturas em 3 graus Fahrenheit
temperature_array + 3
```




    array([87, 81, 84, 78, 82, 78])




```python
# Dividir todas as temperaturas pela metade
temperature_array / 2
```




    array([42. , 39. , 40.5, 37.5, 39.5, 37.5])




```python
# Converter todas as temperaturas de Fahrenheit para Celsius
(5 / 9) * (temperature_array - 32)
```




    array([28.88888889, 25.55555556, 27.22222222, 23.88888889, 26.11111111,
           23.88888889])



**Nota:** Em nenhuma das células acima modificamos o array `temperature_array`! Cada uma dessas expressões criou um _novo_ array.


```python
temperature_array
```




    array([84, 78, 81, 75, 79, 75])



Lembre que para efetivamente alterar `temperature_array`, precisamos reatribuí-lo novamente.


```python
temperature_array = (5 / 9) * (temperature_array - 32)
```


```python
# Agora as temperaturas estão todas em Celsius!
temperature_array
```




    array([28.88888889, 25.55555556, 27.22222222, 23.88888889, 26.11111111,
           23.88888889])



### Aritmética elemento-a-elemento

- Podemos aplicar operações aritméticas a múltiplos arrays, desde que tenham o mesmo comprimento.
- O resultado é calculado **elemento-a-elemento**, o que significa que a operação aritmética é aplicada a um par de elementos de cada array por vez.
- Por exemplo, `a + b` é um array cujo $ij$-ésimo elemento é a soma do $ij$-ésimo elemento de `a` com o $ij$-ésimo elemento de `b`.


```python
a = np.array([1, 2, 3])
b = np.array([-4, 5, 9])
```


```python
a + b
```




    array([-3,  7, 12])




```python
a / b
```




    array([-0.25      ,  0.4       ,  0.33333333])




```python
a ** 2 + b ** 2
```




    array([17, 29, 90])



### Exemplo: visualizações do TikTok 🎬

Nosso mascote, Baby Panda 🐼, fez uma série de cinco vídeos no TikTok chamada "Um dia na vida de um mascote da ciência de dados". O número de visualizações que eles receberam é armazenado na matriz `views` abaixo.


```python
views = np.array([158, 352, 195, 1423916, 46])
views
```




    array([    158,     352,     195, 1423916,      46])



Algumas perguntas que podemos fazer sobre esses dados seguem abaixo.

Qual foi a contagem média de visualizações?


```python
sum(views) / len(views)
```




    284933.4




```python
# O método "mean" existe para arrays, mas não para listas
views.mean()
```




    284933.4



Quantas visualizações os vídeos mais e menos populares receberam?


```python
views.max()
```




    1423916




```python
views.min()
```




    46



Quantas visualizações **acima da média** cada um dos vídeos recebeu?


```python
views - views.mean()
```




    array([-284775.4, -284581.4, -284738.4, 1138982.6, -284887.4])



Quantas visualizações acima da média o vídeo mais visto recebeu?


```python
(views - views.mean()).max()
```




    1138982.6



Existe uma [estimativa](https://www.ngpf.org/blog/question-of-the-day/question-of-the-day-how-much-can-a-creator-on-tiktok-make-if-their-video-receives-1-million-views/) de que o TikTok paga aos seus criadores 0,03 USD a cada 1000 visualizações.

Se isso for verdade, quantos dólares o Bebê Panda ganhou com seu vídeo mais visto? 💸


```python
views
```




    array([    158,     352,     195, 1423916,      46])




```python
views.max() * 0.03 / 1000
```




    42.717479999999995



## Ranges

### Motivação

Frequentemente, precisamos criar arrays do tipo:


```python
days_in_september = np.array([
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 
    13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 
    23, 24, 25, 26, 27, 28, 29, 30
])
```

Podemos então recorrer ao NumPy para uma maneira mais fácil de fazer isso!

### Intervalos
- Um **intervalo** (_range_) é um array de números espaçados uniformemente. Criamos intervalos utilizando `np.arange`.
- A maneira mais genérica de criar um intervalo é utilizar o método `np.arange(start, end, step)`.
- Essa invocação retorna um array tal que:
    - O primeiro número é `start`. **O padrão é tomar `start` = 0.**
    - Todos os números subsequentes são espaçados por `step`, até (mas excluindo) `end`. **O padrão é `step` = 1.**


```python
# Começar em 0, terminar antes de 8, de 1 em 1
np.arange(8)
```




    array([0, 1, 2, 3, 4, 5, 6, 7])




```python
# Começar em 5, terminar antes de 10, de 1 em 1
np.arange(5, 10)
```




    array([5, 6, 7, 8, 9])




```python
# Começar em 3, terminar antes de 32, de 5 em 5
np.arange(3, 32, 5)
```




    array([ 3,  8, 13, 18, 23, 28])




```python
# `step` não precisa ser um número inteiro!
np.arange(-3, 2, 0.5)
```




    array([-3. , -2.5, -2. , -1.5, -1. , -0.5,  0. ,  0.5,  1. ,  1.5])




```python
# Se `step` for um número negativo, contamos de trás para frente
np.arange(1, -10, -3)
```




    array([ 1, -2, -5, -8])



### Exercício ✅

🎉 Parabéns! 🎉 Você ganhou na loteria 💰. Seu pagamento será feito da seguinte maneira: no primeiro dia de setembro, você receberá 0,01. A cada dia seguinte, o valor recebido dobra, então no segundo dia você recebe 0,02, no terceiro dia você recebe 0,04, no quarto dia você recebe 0,08 e assim por diante.

Sabendo que Setembro tem 30 dias, escreva uma **expressão de uma linha** que utilize os números `2` e `30`, junto com a função `np.arange` e o método `.sum()`, para calcular o valor total **em dólares** você será pago em setembro.


```python
...
```




    Ellipsis



## Resumo

- Strings são um tipo de dado (`str`) utilizado para armazenar texto. Para declarar uma string, basta colocar o valor desejado entre aspas simples ou duplas.
- Listas (`List`) e arrays (`array`) são tipos de dados utilizados ​​para armazenar **sequências**.
- As arrays vêm do `NumPy`, e embora não sejam nativas do Python como as listas, são muito mais rápidas e convenientes para operações numéricas.
- Podemos executar operações numéricas em todos os elementos de um array e/ou realizar operações em vários arrays de maneira relativamente simples utilizando **transmissão**/vetorização.
