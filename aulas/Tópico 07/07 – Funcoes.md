# Tópico 7 – Funções e Apply [<img src="images/colag_logo.svg" style="float: right; vertical-align: middle; width: 42px; height: 42px;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2007/07%20%E2%80%93%20Funcoes.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 12px; vertical-align: middle; width: 36px; height: 36px;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2007/07%20%E2%80%93%20Funcoes.ipynb)

Vamos aprender mais sobre as funções no Python, e como aplicar essas funções em um `DataFrame`.

### Resultados Esperados
1. Aprender a definir funções através de`def`.
1. Aprender a aplicar funções em `DataFrame`s através do método `.apply`.

### Referências
- [BPD, Capítulo 12](https://notes.dsc10.com/)
- [CIT, Capítulo 8](https://inferentialthinking.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)


```python
# Importando BabyPandas, Numpy e Matplotlib
import babypandas as bpd
import matplotlib.pyplot as plt
import numpy as np
plt.style.use('ggplot')
```

## Funções

### Definindo novas funções

- Até agora, aprendemos bastante sobre como fazer as seguintes coisas no Python:
    - Manipular arrays, séries e DataFrames.
    - Execute operações em strings.
    - Crie visualizações.

Porém, até aqui, estamos restritos ao uso de funções e métodos _já existentes_ nos módulos que importamos ou nativos ao próprio Python, como por exemplo: `max`, `np.sqrt`, `len`, `.groupby`, `.assign` e `.plot`, dentre outros.

#### Motivação

Suponha que você dirija até um restaurante 🥘 em Ouro Preto, localizado a exatamente 100 quilômetros de distância.

- Nos primeiros 80 quilômetros, você dirige a 80 quilômetros por hora.
- Nos últimos 20 quilômetros, você dirige a 60 quilômetros por hora.

- **Pergunta:** Qual é a sua **velocidade média** durante a viagem?

🚨 A resposta não é 70 quilômetros por hora! Você precisa usar o fato de que $\text{velocidade} = \frac{\text{distancia}}{\text{tempo}}$.

Nesse caso específico, temos 

$$\text{velocidade média} = \frac{\text{distância total}}{\text{tempo total}} = \frac{80 + 20}{\text{tempo}_1 + \text{tempo}_2} \: \text {km/hora}$$

No segmento 1, quando você dirigiu 80 quilômetros a 80 quilômetros por hora, você dirigiu por $\frac{80}{80}$ horas:

$$\text{velocidade}_1 = \frac{\text{distância}_1}{\text{tempo}_1}$$

$$80 \: \text{km/hora} = \frac{80 \text{km}}{\text{tempo}_1} \implies \text{tempo}_1 = \frac{80}{80} \: \text{horas} = 1 \: \text{hora}$$

Da mesma forma, no segmento 2, quando você dirigiu 20 quilômetros a 60 quilômetros por hora, você dirigiu por $\text{tempo}_2 = \frac{20}{60} \text{ horas} = \frac{1}{3} \: \text{horas}$.

Finalmente, temos então

\begin{align*}
    \text{velocidade média} &= \frac{80 + 20}{\frac{1}{1} + \frac{1}{3}} \: \text{km/hora} \\
    &= 100 \cdot \frac{1}{\frac{1}{1} + \frac{1}{3}} \text{km/hora} \\ 
    &= 100 \cdot \frac{1}{\frac{3 + 1}{3}} \: \text{km/hora} \\ 
    &= 100 \cdot \frac{3}{4} \: \text{km/hora} \\ 
    &= 75 \text{km/hora}
\end{align*}

#### Exemplo de função: Média harmônica

A **média harmônica** ($\text{HM}$) de dois números positivos, $a$ e $b$, é definida como

$$\text{HM} = \frac{2}{\frac{1}{a} + \frac{1}{b}}$$

Geralmente, HM é utilizada para encontrar a média de múltiplas **taxas**.

Encontrar a média harmônica de 80 e 60 não é difícil:


```python
2 / (1 / 1 + 1 / 3)
```




    1.5



Mas e se quisermos determinar a média harmónica de 80 e 70? 80 e 90? 20 e 40? ]

**Isso exigiria muitas operações do tipo "copiar e colar", o que torna nosso código propenso a erros.**

Uma ótima solução para esses casos é **definir** nossa própria função de "média harmônica"!


```python
def harmonic_mean(a, b):
    return 2 / (1 / a + 1 / b)
```


```python
harmonic_mean(1, 3)
```




    1.5




```python
harmonic_mean(1, 5)
```




    1.6666666666666667



Podemos responder nossa pergunta anterior com nossa função de média harmônica:


```python
100*harmonic_mean(1, 3)/2
```




    75.0



Observe que só tivemos que especificar como calcular a média harmônica _uma única_ vez!

### Definindo funções no Python

- As funções são uma forma de dividir nosso código em pequenas subpartes.
- Isso evita repetição, e melhora a legibilidade do código.
- Definimos novas funções no Python através do seguinte padrão:

````py
def <function name>(<arguments>):
    <body>
    <return>
````

Vamos agora explicar cada um dos detalhes dessa estrutura.

### Funções são "receitas"

- As funções recebem entradas, conhecidas como **argumentos** (\<arguments>), executam código (\<body>) e produzem uma saída (\<return>).
- A beleza das funções é que, na maior parte das vezes, **você não precisa saber como elas são implementadas para usá-las!**
- Esta é a premissa da ideia de **abstração** na Ciência da Computação – você aprenderá mais sobre isso em outros cursos.


```python
harmonic_mean(1, 1)
```




    1.0




```python
harmonic_mean(1, 3)
```




    1.5




```python
harmonic_mean(1, 2)
```




    1.3333333333333333



### Argumentos

Como um exemplo, vamos definir uma função `triple` abaixo, que recebe um argumento `x` e triplica esse argumento.


```python
def triple(x):
    return x * 3
```

Quando chamamos `triple` com o **argumento** 5, por exemplo, podemos fingir que há uma primeira linha "invisível" no corpo de `triple` que diz `x = 5`.


```python
triple(5)
```




    15



Observe que, apesar de implicitamente assumirmos que `x` é uma variável numérica, como não especificamos isso na definição de `triple`, seus argumentos podem ser de qualquer tipo!


```python
triple('triton ')
```




    'triton triton triton '




```python
triple([1, 2])
```




    [1, 2, 1, 2, 1, 2]



#### Número de argumentos

- As funções podem ter qualquer número de argumentos (inclusive 0!)

Por exemplo, a função `greeting` abaixo não aceita argumentos!


```python
def greeting():
    return 'Hi! 👋'
```


```python
greeting()
```




    'Hi! 👋'




```python
# Descomente e execute
# greeting(42)
```

### As funções não são executadas até que você as chame!

- O corpo (\<body>) de uma função não é executado até que você use/chame/invoque  a função.

Por exemplo, vamos definir abaixo uma função `where_is_the_error`:


```python
def where_is_the_error(something):
    '''You can describe your function within triple quotes. For example, this function 
    illustrates that errors don't occur until functions are executed (called).'''
    return (1 / 0) + something
```

Até executarmos `where_is_the_error`, não veremos nenhuma mensagem de erro!


```python
# Descomente e execute
# where_is_the_error(5)
```

#### Exemplo: `first_name`

- Vamos definir abaixo uma função chamada `first_name`.
- Queremos que essa função receba o nome completo de alguém e retorne seu primeiro nome.

Um exemplo desse comportamento desejado seria:
```py
>>> first_name('Flavio Figueiredo')
'Flavio'
```

#### Estratégia geral para escrever funções:

1. Primeiro, tentamos fazer com que o comportamento esperado funcione em um único exemplo.
2. Em seguida, encapsulamos esse comportamento dentro de uma função, tomando os parâmetros do exemplo como argumentos


```python
'Flavio Figueiredo'.split(' ')[0]
```




    'Flavio'




```python
def first_name(full_name):
    '''Returns the first name given a full name.'''
    return full_name.split(' ')[0]
```


```python
first_name('Flavio Figueiredo')
```




    'Flavio'




```python
first_name('Uriel Moreira Silva')
```




    'Uriel'



Perfeito! 👍

### Retorno

- A palavra-chave `return` especifica qual deve ser a saída para a função definida, isto é, qual será o valor resultante de uma chamada da função.
- A maioria das funções que escrevemos usará `return`, mas isso não é obrigatório.
- Existe uma diferença fundamental entre `print` e `return`: utilizando `return`, podemos _atribuir_ o resultado da função a uma variável, e depois utilizar esse resultado posteriormente no nosso código.

Como exemplo, considere a seguinte função, que se utiliza do Teorema de Pitágoras para calcular o tamanho da hipotenusa de um triângulo retângulo com base `a` e altura `b`:


```python
# Teorema de Pitágoras, sem retorno
def pythagorean(a, b):
    '''Computes the hypotenuse length of a triangle with legs a and b.'''
    c = (a ** 2 + b ** 2) ** 0.5
    print(c)
```


```python
pythagorean(3, 4)
```

    5.0
    

Sem especificar nenhum retorno, a função printa o resultado calculado, mas não atribui esse resultado a nenhum objeto:


```python
x = pythagorean(3, 4)
```

    5.0
    


```python
# Nenhuma saída!
x
```


```python
# Descomente e execute.
# x + 10
```

Se reescrevermos a função especificando um `return`, ela agora atribui o resultado corretamente:


```python
def better_pythagorean(a, b):
    '''Computes the hypotenuse length of a triangle with legs a and b, and actually returns the result.'''
    c = (a ** 2 + b ** 2) ** 0.5
    return c
```


```python
x = better_pythagorean(3, 4)
x
```




    5.0




```python
x + 10
```




    15.0



Note que como agora não executamos mais o `print` dentro da função, precisamos printar o objeto caso queiramos saber seu resultado!


```python
y = better_pythagorean(1, 1)
```


```python
# Descomente e execute.
# y
```

#### Alto lá! 🛑

- Depois que uma função executa uma instrução `return`, ela assume que o código terminou, e ignora tudo o que vem depois!


```python
def motivational(quote):
    return 0
    print("Uma frase motivacional:", quote)
```


```python
motivational('Sem as lutas não há derrotas.')
```




    0



- Em alguns contextos específicos, podemos utilizar dessa propriedade do `return`, principalmente em combinação com as _estruturas condicionais_ que veremos no próximo tópico. 

### Escopo

- Os nomes escolhidos como argumentos ou parâmetros de uma função são relevantes _apenas_ para essa função, e não afeta o resto do código.
- Damos a isso o nome de **escopo local**.

Observe o exemplo abaixo:


```python
def what_is_awesome(s):
    return s + ' is awesome!'
```


```python
what_is_awesome('data science')
```




    'data science is awesome!'




```python
# Descomente e execute
# s
```


```python
s = 'FCD'
```


```python
what_is_awesome('data science')
```




    'data science is awesome!'




```python
s
```




    'FCD'



Apesar de termos `s` tanto fora quanto dentro do escopo da função, o Python consegue diferenciar ambos como sendo objetos diferentes! 

Nota: existem maneiras de declarar e operar variáveis no **escopo global**, de maneira a afetar todas as partes do seu código (inclusive dentro e fora de funções), mas você verá isso em detalhes somente em outros cursos!

## Aplicando funções a DataFrames

### Exemplo: alunos de FCD

O DataFrame `df` abaixo contém os primeiros nomes de todos os alunos do Bacharelado de Ciência de Dados de 2024/1 matriculados em Fundamentos de Ciência de Dados:


```python
nomes = 'ANNY \
ARTHUR \
ARTHUR \
CAIO \
CAROLINA \
CLARA \
DANIELLE \
EDUARDO \
EDUARDO \
EMANUEL \
ENZO \
FELIPE \
FELIPE \
FRANCISCO \
GABRIEL \
GABRIEL \
GABRIELLY \
GAEL \
GUILHERME \
GUILHERME \
GUSTAVO \
ISAAC \
JOAO \
JOAO \
KARINA \
LETICIA \
LETICIA \
LIVIA \
LORRANY \
LUCAS \
LUIS \
MARCO \
MATEUS \
MATEUS \
MATHEUS \
RAIZA \
RENATO \
SOPHIA \
THAYRELAN \
VICTOR'
```


```python
df = bpd.DataFrame().assign(
    nome = nomes.split()
)

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>nome</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ANNY</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ARTHUR</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ARTHUR</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CAIO</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CAROLINA</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>35</th>
      <td>RAIZA</td>
    </tr>
    <tr>
      <th>36</th>
      <td>RENATO</td>
    </tr>
    <tr>
      <th>37</th>
      <td>SOPHIA</td>
    </tr>
    <tr>
      <th>38</th>
      <td>THAYRELAN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>VICTOR</td>
    </tr>
  </tbody>
</table>
<p>40 rows × 1 columns</p>
</div>



#### Qual a primeira letra mais comum entre os nomes dos discentes de FCD?

- **Problema**: Não conseguimos responder essa pergunta agora, pois não temos uma coluna com a primeira letra de cada nome!
- **Solução**: Criar uma função para obter uma columa com a primeira letra de cada nome, e depois agrupar por essa coluna!.


```python
def primeira_letra(nome):
    return nome[0]
```


```python
primeira_letra('FLAVIO')
```




    'F'



Utilizando o método `.iloc`, vamos agora testar essa função em alguns alunos do DataFrame `df`:


```python
df.get('nome').iloc[0]
```




    'ANNY'




```python
primeira_letra(df.get('nome').iloc[0])
```




    'A'




```python
df.get('nome').iloc[1]
```




    'ARTHUR'




```python
primeira_letra(df.get('nome').iloc[1])
```




    'A'



Funciona!

Porém, como estamos interessados em fazer isso para _todos os alunos_ (e depois ainda resumir a informação resultante utilizando alguma estatística descritiva), essa abordagem de aplicar a função 1-a-1 é bem ineficiente. 

### O método `.apply`

- Podemos **aplicar** uma função a _cada_ elemento da coluna `column_name` no DataFrame `df` utilizando

```python
    df.get(column_name).apply(function_name)
````

No nosso exemplo, fazemos:


```python
df.get('nome').apply(primeira_letra)
```




    0     A
    1     A
    2     A
    3     C
    4     C
         ..
    35    R
    36    R
    37    S
    38    T
    39    V
    Name: nome, Length: 40, dtype: object



e, para saber qual a primeira letra do nome é mais comum, basta fazer um `.groupby`:


```python
df_2 = df.assign(primeira_letra = df.get('nome').apply(primeira_letra))
df_2.groupby('primeira_letra').size()
```




    primeira_letra
    A    3
    C    3
    D    1
    E    4
    F    3
        ..
    M    4
    R    2
    S    1
    T    1
    V    1
    Length: 15, dtype: int64



e depois uma consulta apropriada!


```python
k = (df_2.groupby('primeira_letra').size()).max()
print(k)
```

    7
    


```python
df_2.groupby('primeira_letra').size()[df_2.groupby('primeira_letra').size() == 7]
```




    primeira_letra
    G    7
    dtype: int64




```python
# Uma maneira muito mais eficiente de fazer isso:
df = df.assign(
    primeira=df.get('nome').apply(primeira_letra)
)
print(df)

letra_count = (df.
               groupby('primeira').
               size().
               sort_values(ascending = False)
)
letra_count
```

             nome primeira
    0        ANNY        A
    1      ARTHUR        A
    2      ARTHUR        A
    3        CAIO        C
    4    CAROLINA        C
    ..        ...      ...
    35      RAIZA        R
    36     RENATO        R
    37     SOPHIA        S
    38  THAYRELAN        T
    39     VICTOR        V
    
    [40 rows x 2 columns]
    




    primeira
    G    7
    L    6
    E    4
    M    4
    A    3
        ..
    I    1
    K    1
    S    1
    T    1
    V    1
    Length: 15, dtype: int64



#### Mais sobre o `.apply`

- O método `.apply` é um método de `Series`s, **não** de `DataFrame`s!
- Da mesma maneira, a saída do `.apply` também é uma `Series`.

- Nota: ao utilizar o `.apply`, devemos passar _apenas o nome_ da função – não a chame!
    - Bom ✅: `.apply(function)`.
    - Ruim ❌: `.apply(function())`.


```python
# Descomente e execute
# df.get('nome').apply(primeira_letra())
```

#### Utilizando o `.apply` com funções pré-existentes

- Naturalmente, o método `.apply` funciona com qualquer outra função/método que já estejam definidos nativamente no Python, ou que forem importados por outros módulo.

No nosso exemplo dos nomes dos alunos, para encontrar o comprimento de cada nome podemos usar a função `len`:


```python
df.assign(comprimento = df.get('nome').apply(len))
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>nome</th>
      <th>primeira</th>
      <th>comprimento</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ANNY</td>
      <td>A</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ARTHUR</td>
      <td>A</td>
      <td>6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ARTHUR</td>
      <td>A</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CAIO</td>
      <td>C</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CAROLINA</td>
      <td>C</td>
      <td>8</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>35</th>
      <td>RAIZA</td>
      <td>R</td>
      <td>5</td>
    </tr>
    <tr>
      <th>36</th>
      <td>RENATO</td>
      <td>R</td>
      <td>6</td>
    </tr>
    <tr>
      <th>37</th>
      <td>SOPHIA</td>
      <td>S</td>
      <td>6</td>
    </tr>
    <tr>
      <th>38</th>
      <td>THAYRELAN</td>
      <td>T</td>
      <td>9</td>
    </tr>
    <tr>
      <th>39</th>
      <td>VICTOR</td>
      <td>V</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
<p>40 rows × 3 columns</p>
</div>



### Exercício ✅

Nas duas células abaixo, crie um gráfico de barras para a _primera_ e _última_ letra de cada nome, respectivamente.

Quais são suas conclusões?


```python
...
```




    Ellipsis




```python
...
```




    Ellipsis



## Resumo

- As funções são uma forma de dividir nosso código em pequenas subpartes.
- As funções evitam repetição, melhoram a legibilidade e a organização do código, e podem nos permitir _abstrair_ certos elementos.
- O método `.apply` nos permite aplicar uma função em cada elemento de uma `Serie`.
