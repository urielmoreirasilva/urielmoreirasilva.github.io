# Tópico 15 – Testes de Hipóteses [<img src="images/colag_logo.svg" style="float: right; margin-right: 0%; vertical-align: middle; width: 6.5%;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2015%20%E2%80%93%20Testes%20De%20Hip%C3%B3teses%2F15%20%E2%80%93%20TestesDeHipoteses.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 0%; vertical-align: middle; width: 3.25%;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2015%20%E2%80%93%20Testes%20De%20Hip%C3%B3teses%2F15%20%E2%80%93%20TestesDeHipoteses.ipynb)

Nessa aula, voltamos nossa atenção para uma pergunta fundamental em Ciência de Dados: "como testar se um conjunto de hipóteses feitas sobre nossos dados é adequado ou não?".

### Resultados Esperados 

1. Aprender a definir o que são modelos estatísticos, e a formular problemas práticos em Ciência de Dados como modelos.
1. Introduzir o conceito de significância estatística, e a caracterização da "quantidade de erro aleatório" que estamos dispostos a permitir em situações práticas.
1. Introduzir as noções básicas de Testes de Hipóteses, e a utilizar essa metodologia para testar a adequação de diferentes modelos estatísticos em problemas de análises de dados.

### Referências
- [CIT, Capítulo 11](https://inferentialthinking.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)


```python
## Imports para esse tópico (note que aqui temos um novo módulo nessa lista: o SciPy!)
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import scipy as scipy
plt.style.use('ggplot')

## Opções de como printar objetos do Numpy e do Pandas
np.set_printoptions(threshold = 20, precision = 2, suppress = True)
pd.set_option("display.max_rows", 7)
pd.set_option("display.max_columns", 8)
pd.set_option("display.precision", 2)
```

## Modelos Estatísticos

### Definição básica

- Informalmente, um **modelo estatístico** consiste de um _conjunto de hipóteses_ que fazemos sobre o processo que gerou nossos dados.

Exemplos simples de modelos são:
- Moedas "justas" têm probabilidade igual de resultarem em cara ou coroa.
- Dados "justos" têm probabilidade igual de resultarem em cada um dos lados.
- A gravidez de uma fêmea Golden Retriever pode resultar em 1 até 14 (!) filhotes, com média entre 7 e 8.

- Um dos principais objetivos em Inferência Estatística é **aferir a qualidade de um modelo**.
- Em outras palavras, buscamos aferir **o _quão bem_ um modelo explica a "realidade"** refletida nos dados.  

- Conforme aprendemos até agora, a maioria dos problemas em Inferência pode ser resolvido através de alguma teoria (baseada em Matemática e Probabilidade), e/ou com técnicas de simulação.
- Em ambos os casos, vamos sempre **assumir que o nosso modelo seja verdadeiro**, e então calcular as frequências/probabilidades com as quais os padrões observados nos nossos dados ocorreriam sob esse modelo.

- Em geral, o processo de verificar se um modelo é adequado ou não para um certo conjunto de dados é denominado de **teste de hipótese**. Definiremos essa noção mais formalmente abaixo.

### Exemplo: lançamento de uma moeda

Como exemplo motivador, suponha que queiramos decidir se uma certa moeda é "justa", isto é, se no lançamento dessa moeda a probabilidade de uma cara é igual a probabilidade de uma coroa (1/2, ou 50%).

Para verificar isso, lançamos a moeda $n = 400$ vezes, e anotamos os resultados obtidos.


```python
flips_400 = pd.read_csv('data/flips-400.csv')
flips_400
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
      <th>flip</th>
      <th>outcome</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Tails</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Tails</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Tails</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>397</th>
      <td>398</td>
      <td>Heads</td>
    </tr>
    <tr>
      <th>398</th>
      <td>399</td>
      <td>Heads</td>
    </tr>
    <tr>
      <th>399</th>
      <td>400</td>
      <td>Tails</td>
    </tr>
  </tbody>
</table>
<p>400 rows × 2 columns</p>
</div>




```python
flips_400.groupby('outcome').count()
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
      <th>flip</th>
    </tr>
    <tr>
      <th>outcome</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Heads</th>
      <td>188</td>
    </tr>
    <tr>
      <th>Tails</th>
      <td>212</td>
    </tr>
  </tbody>
</table>
</div>



- Naturalmente, podemos então perguntar: **esse resultado é consistente ou não** com o nosso "modelo", isto é, esse resultado condiz com a _hipótese_ de que a moeda é justa? 🤔
- Em outras palavras, quão _provável_ (ou _improvável_) seria obtermos 188 caras em 400 lançamentos de uma moeda justa?

## Significância Estatística

- Antes de introduzirmos o ferramental necessário para responder à pergunta acima, primeiramente vamos refletir um pouco sobre outro ponto:

> Se esperamos _exatamente_ uma proporção de 50% de caras nos lançamentos da moeda do exemplo anterior, quais as proporções $p$ de caras aceitaríamos como **estatisticamente iguais** a 50%?

> Analogamente, o que nesse caso seria uma "diferença inaceitável" entre $p$ e 50% para decidirmos que a moeda não é justa?  

- Para responder perguntas como essa, frequentemente utilizamos o conceito de **significância estatística**. 

- Basicamente, um resultado é considerado **estatisticamente significante** se sua ocorrência por pura chance/aleatoriedade/"coincidência" é considerada **baixa**.

- Mais formalmente, um evento é considerado estatisticamente significante se sua probabilidade é **menor** do que um certo valor $\alpha \in (0, 1)$.
- Denominamos $\alpha$ então de **nível de significância**. Um nível de significância muito comum utilizado na prática é $\alpha = 5\%$.

- O _complementar_ do nível de significância $\alpha$ é o nível de confiança $\gamma$ que vimos anteriormente no Tópico 14, isto é, temos sempre $\alpha + \gamma = 1$.
- Escolher um nível de significância de $\alpha = 5\%$ é então equivalente a escolher um nível de confiança de $\gamma = 1 - \alpha = 95\%$, por exemplo.

### De volta ao exemplo da moeda

- _Supondo que a moeda seja justa_, qual a probabilidade de ocorrerem entre 188 e 212 caras em $n = 400$ lançamentos?

- Omitindo as tecnicalidades, essa probabilidade pode ser calculada como (lembre do Triângulo de Pascal!)

\begin{equation*}
    \sum^{212}_{k = 188} {n \choose k} \left(\frac{1}{2}\right)^k \left(\frac{1}{2}\right)^{n-k} \simeq 0.7888 = 78.88\%
\end{equation*}

- No Python, podemos calcular essa probabilidade através da _função de massa de probabilidade_ da _Distribuição Binomial_, `scipy.stats.binom.cdf`.
    - Essa é outra distribuição que você verá nos próximos cursos!
    - Os parâmetros dessa função são o "número de sucessos" `k` $(k = 0, 1, \ldots, n)$, o "número de experimentos" `n` $(n \in \mathbb{N})$ e a "probabilidade de sucesso de cada experimento", `p` $(0 < p < 1)$.


```python
prob = 0
n = 400
p = 0.5

for k in np.arange(188, 212 + 1, 1):
    prob = prob + scipy.stats.binom.pmf(int(k), n, p)

round(prob, 4)
```




    0.7888



- Dessa forma, _se a moeda for justa_, a probabilidade de obtermos um número de caras entre 188 e 212 é bem alta!
- Analogamente, a probabilidade de obtermos um número de caras **menor** que 188 e **maior** que 212 será então igual a $1 - 0.7888 \simeq 0.2112$, ou $21.12\%$.


```python
round(1 - 0.7888, 4)
```




    0.2112



- O resultado acima nos diz então que, ainda que a moeda seja justa, a probabilidade de obtermos algo em torno de 188 a 212 caras em $n = 400$ lançamentos é _bem razoável_.
- Em outras palavras, como $188/400 = 0.47$ e $212/400 = 0.53$, desvios de até $3\%$ na proporção esperada de caras ($50\%$) em $n = 400$ lançamentos de uma moeda justa ocorrem com uma probabilidade de $21.12\%$.

- Concluímos então que uma ocorrência de 188 caras em 400 lançamentos **não é considerada significativa** a um nível $\alpha = 5\%$!

- Agora, o que seria então considerado estatisticamente significante nesse problema? 🤔

Se tívessemos observado, por exemplo, 181 caras:


```python
prob = 0
n = 400
p = 0.5

## Número observado de caras (19 *a menos* ou *a mais* que o "esperado" = 200)
heads_l = 181
heads_u = 219

for k in np.arange(heads_l, heads_u + 1, 1):
    prob = prob + scipy.stats.binom.pmf(int(k), n, p)

round(1 - prob, 4)
```




    0.051



Ainda não!

Talvez 180 caras?


```python
prob = 0
n = 400
p = 0.5

## Número observado de caras (20 *a menos* ou *a mais* que o "esperado" = 200)
heads_l = 180
heads_u = 220

for k in np.arange(heads_l, heads_u + 1, 1):
    prob = prob + scipy.stats.binom.pmf(int(k), n, p)

round(1 - prob, 4)
```




    0.0402



Agora sim! 👍

- Ao nível de significância de $\alpha = 5\%$, observar de 181 a 219 caras (isto é, desvios de até 4.75% na proporção esperada de caras) ainda seria considerado "aceitável" (isto é, _não-significante_) nesse caso.
- Precisaríamos de um resultado mais _extremo_ (equivalente a desvios maiores ou iguais a 5% na proporção esperada de caras) para ser considerado estatisticamente significante, como por exemplo 180 caras ou menos, ou 220 caras ou mais. 

> Para concluir nosso exemplo, lembrando da complementariedade entre o nível de confiança $\gamma$ e o nível de significância $\alpha$, podemos obter a _mesma resposta acima_ através de um intervalo de 95% de confiança para a proporção populacional $p$ de caras em uma moeda justa, que podemos calcular _exatamente_ através de:


```python
scipy.stats.binom.interval(0.95, 400, .5)
```




    (180.0, 220.0)



- Voltaremos à relação entre $\gamma$ e $\alpha$ mais adiante abaixo, mas note que o intervalo e as probabilidades calculados de maneira exata acima são dados aqui apenas para comparação. 
- Para os propósitos desse curso, **vamos calcular todas essas quantidades através de simulação**! 

### Exercício ✅

Você acha que o mesmo resultado acima valeria para $n = 40$ ou $n = 4.000$ lançamentos? Por que? Faça as alterações necessárias nas células de código abaixo e comente brevemente sobre os resultados na célula de texto seguinte.

**Importante**: Calcule antes o número de caras equivalente a 188 em $n = 400$ quando $n = 40$ e $n = 4.000$.


```python
# ## Descomente e execute!
# prob = 0
# n = ...
# p = 0.5

# for k in np.arange(..., ... + 1, 1):
#     prob = prob + scipy.stats.binom.pmf(int(k), n, p)

# round(prob, 4)
```


```python
# ## Descomente e execute!
# n = ...
# scipy.stats.binom.interval(0.95, n, .5)
```

> ...

## Testes de Hipóteses

### Noções básicas

- Podemos definir um **Teste de Hipóteses** como um procedimento em que testamos a **hipótese de que o nosso modelo esteja correto** contra a **hipótese de que o nosso modelo não esteja correto**.
    - A primeira hipótese, isto é, de que o nosso modelo esteja correto, é denominada de **hipótese nula**.
    - A segunda hipótese, isto é, de que o nosso modelo não esteja correto, é denominada de **hipótese alternativa**.
- A **aceitação** de uma hipótese em detrimento da outra leva à **rejeição** da outra hipótese.

- **Muito importante:** Embora estejamos definindo os Testes de Hipóteses e nossos modelos em termos como "aceitação" e "correto", note que na verdade o procedimento apenas nos diz se o modelo postulado é _condizente_ com os padrões observados nos nossos dados.
- Dessa forma, como em geral o processo gerador dos dados é _aleatório_, podemos aceitar a hipótese de um modelo esteja correto (ou errado) por _pura aleatoriedade_, sem que na verdade o modelo seja _verdadeiramente_ correto (ou errado)!
- Essa indeterminação é **inerente à qualquer processo de decisão sob aleatoriedade**, e vale para _todas_ as técnicas que utilizamos em Inferência Estatística, exceto em situações triviais.

- Em vista da observação acima, uma outra maneira de pensarmos no nível de significância é como um **nível máximo permitido** para a influência da incerteza/aleatoriedade sobre o nosso experimento/processo gerador de dados.
- Equivalentemente, $\alpha$ representa **a frequência máxima com a qual nosso modelo (ainda que esteja correto) possa ser rejeitado por um teste de hipóteses**.

> No exemplo acima, embora 400 lançamentos de uma moeda justa possam _de fato_ produzir menos de 180 caras (ou mais de 220), a _probabilidade_ com a qual isso ocorre é menor que $\alpha$.

> Reiterando sobre esse ponto, _ainda que nosso modelo esteja correto_, a probabilidade desse resultado ocorrer por pura aleatoriedade é _tão baixa_ que consideramos ser _mais plausível_ que nosso modelo esteja errado! 

- Finalmente, como para cada hipótese nula fazemos um teste separado, é possível que **aceitemos várias hipóteses nulas diferentes** (e, logo, **vários modelos diferentes**) para os _mesmos dados_.
- Embora existam algumas maneiras de contornar esse problema (você verá isso nos próximos cursos!), essa é uma característica **inerente** à várias outras técnicas de Inferência Estatística.
- Não entraremos em mais detalhes, mas lembre da nossa discussão anterior sobre **igualdade estatística**: com 95% de confiança, todas as proporções populacionais $p \in (0.45, 0.55)$ são _igualmente prováveis_ (inclusive $p = 0.50$!) nesse experimento.

### Hipóteses nulas e alternativas

Vamos agora formalizar as noções de hipóteses nulas e alternativas introduzidas acima.

- No exemplo da moeda justa, nossa hipótese nula é: "a moeda é justa".
- Analogamente, nossa hipótese alternativa é "a moeda não é justa".

- Em um teste de hipóteses, a hipótese nula deve ser um **modelo de probabilidade bem definido** sobre o processo que gerou nossos dados.
- A razão para isso é que precisamos poder calcular todas as probabilidades e frequências sobre as quais estamos interessados, não só do ponto de vista _teórico/matemático_, mas também do ponto de vista _prático/computacional_.
- Em outras palavras: precisamos definir bem um modelo para simular desse modelo!
- Usualmente denotamos a hipótese nula por $H_0$.

Mais uma vez voltando ao nosso exemplo, uma possível hipótese nula aqui seria $H_0\!\!: p = 0.50$, onde $p$ representa a probabilidade do lançamento de uma moeda resultar em cara (nosso **parâmetro**).

Tecnicamente, para que esse modelo realmente esteja bem definido, definimos _sob $H_0$_ uma variável aleatória $X$ tomando valores no espaço amostral $\{H, T\}$ com probabilidades $p_0 = 0.50$ e $1 - p_0 = 0.50$, respectivamente.

- Por outro lado, a **hipótese alternativa** representa uma _visão diferente_ (e _usualmente_ complementar, para que então o **teste** também esteja bem definido) do processo que gerou nossos dados.
- Dessa forma, a hipótese alternativa **não precisa ser específica**, uma vez que basta a hipótese nula _ser rejeitada_ para que a hipótese alternativa _seja aceita_.
- Usualmente denotamos a hipótese alternativa por $H_1$. 

Retornando ao exemplo da moeda, uma possível hipótese alternativa seria $H_1\!\!:p \neq 0.50$.

Note que essa é uma hipótese bem vaga, assim como "a moeda não é justa", pois _qualquer valor_ para a proporção de caras diferente de $p_0 = 0.50$ satisfaz a hipótese alternativa!

### Estatísticas de teste

- Uma vez definidas $H_0$ e $H_1$, começamos então nossa inferência **supondo que a hipótese nula $H_0$ seja verdadeira**.

- **Sob $H_0$**, calcularemos uma quantidade que denominaremos de **estatística de teste** (usualmente denotada por $T$).
- Como o próprio nome implica, essa será uma **estatística** (e logo calculada com base na amostra) utilizada para realizarmos nosso teste.
- Em essência, a estatística de teste objetiva medir a evidência _a favor_ (ou _contra_) $H_0$. 

- Como aqui vamos utilizar simulação para conduzir nossos testes, teremos então _uma estatística de teste para cada amostra_ produzida sob $H_0$.
- Com base em um número grande de amostras, produzimos então uma aproximação para a distribuição de probabilidade da estatística de teste $T$, isto é, uma distribuição amostral empírica de $T$.
- Assim, nosso teste basicamente consiste em verificarmos se o valor observado para a nossa estatística de teste (usualmente denotado por $T_{obs}$) é _estatisticamente significante_ nessa distribuição amostral empírica.
- De maneira análoga como fizemos anteriormente para calcularmos Intervalos de Confiança via Bootstrap, a verificação de significância nesse caso consiste essencialmente em verificar à _qual percentil dessa distribuição_ corresponde o valor de $T_{obs}$!

### Simulando sob $H_0$

- Voltando ao nosso exemplo anterior, podemos simular `n` lançamentos de uma moeda justa utilizando a função `np.random.binomial(n, 0.5)`.
- O resultado de `np.random.binomial(n, 0.5)` é equivalente ao de `np.random.multinomial(n, [0.5, 0.5])[0]` que vimos no Tópico 11.
- Como estamos interessados na proporção de caras, nossa estatística de teste $T$ será o número de caras em cada simulação.


```python
np.random.seed(38)
np.random.binomial(400, 0.5)
```




    195




```python
np.random.seed(38)
np.random.multinomial(400, [0.5, 0.5])[0]
```




    195



- Calculamos então $T$ para cada amostra simulada, anotamos o valor correspondente e, dessa forma, construímos uma distribuição empírica para a estatística de teste $T$ sob $H_0$.
- Lembre mais uma vez que aqui _simulamos sob $H_0$_, uma vez que simular sob $H_1$ seria _impossível_ (existem infinitos não-enumeráveis valores de $p_0$ que satisfazem a definição de "moeda injusta").


```python
## Simulando (sob H_0) a distribuição de T
# ----

## Fixando a semente aleatória (para garantir reproducibilidade)
np.random.seed(42)

## Loop principal
results = np.array([])
for i in np.arange(10000):
    result = np.random.binomial(400, 0.5)
    results = np.append(results, result)
results
```




    array([193., 194., 202., ..., 201., 201., 192.])



### Distribuição amostral empírica de T

Vamos agora visualizar a distribuição empírica da estatística de teste $T$ sob $H_0$:


```python
(pd.DataFrame({"results" : results})
 .plot(kind = 'hist', bins = np.arange(160, 240, 4),
       density = True, ec = 'w', figsize=(10, 5),
       title='Distribuição Empírica do Número de Caras em $n = 400$ Lançamentos de uma Moeda Justa'));
plt.axvline(188, color = 'black', linewidth = 4, label = 'T_obs = 188')
plt.legend()
plt.ylabel("Densidade");
```


    
![png](15%20%E2%80%93%20TestesDeHipoteses_files/15%20%E2%80%93%20TestesDeHipoteses_67_0.png)
    


- Se tívessemos observado $T'_{obs} = 200$ caras, naturalmente aceitaríamos $H_0$, isto é, a hipótese de que nossa moeda é justa.

- Analogamente, rejeitaríamos $H_0$ (isto é, concluiríamos que nossa moeda é injusta) se:
    - observássemos "poucas caras", ou
    - observássemos "muitas caras".

- Mas como decidimos o que seriam "poucas" e "muitas" nesse contexto? 🤔

### Exercício ✅

Faça as alterações necessárias nas células de código abaixo para simular da distribuição da proporção de caras $p_{obs}$ sob $H_0$ e visualizar essa distribuição em um histograma.


```python
# ## Descomente e execute!

# ## Simulando (sob H_0) a distribuição de T
# # ----

# ## Fixando a semente aleatória (para garantir reproducibilidade)
# np.random.seed(42)

# ## Loop principal
# results = np.array([])
# for i in np.arange(10000):
#     result = np.random.binomial(400, 0.5)/...
#     results = np.append(results, result)
# results
```


```python
# ## Descomente e execute!
# (pd.DataFrame({"results" : results})
#  .plot(kind = 'hist', bins = np.arange(160/..., 240/..., 4/...),,
#        density = True, ec = 'w', figsize = (10, 5),
#        title='Distribuição Empírica da Proporção de Caras em $n = 400$ Lançamentos de uma Moeda Justa'));
# plt.axvline(188/..., color = 'black', linewidth = 4, label = 'p_obs = ...')
# plt.legend()
# plt.ylabel("Densidade");
```

### Valores Críticos e Regiões de Aceitação

- Dada a nossa discussão acima sob significância estatística, seria natural **rejeitarmos $H_0$ se a probabilidade de observarmos $T_{obs}$ for muito baixa**.

- No exemplo anterior, vimos (teoricamente) que a probabilidade de que $T \leq 180$ ou que $T \geq 220$ é pouco menor que 5%.
- Dessa forma, valores **tão extremos** quanto $T \leq 180$ ou $T \geq 220$ são considerados **estatisticamente significantes** (ao nível de $\alpha = 5\%$), e logo _indicativos_ de que $H_0$ não é verdadeira.

Na distribuição simulada anteriormente, podemos encontrar esses valores como os percentis 2.5% e 97.5% da distribuição empírica de $T$, respectivamente:


```python
np.percentile(results, 2.5)
```




    180.0




```python
np.percentile(results, 97.5)
```




    220.0



- Ao nível de $\alpha = 5\%$ de significância, dizemos então que $c_1 = 180$ e $c_2 = 220$ são os **níveis críticos** (ou valores críticos) desse teste de hipóteses.

- Mais formalmente, os **níveis críticos** de um teste são valores $c_1 < c_2$ tais que

> **$H_0$ é rejeitada ao nível $\alpha\%$ de significância** caso $T_{obs} < c_1$ _ou_ $T_{obs} > c_2$

ou, equivalentemente,

> **$H_0$ é aceita ao nível $\alpha\%$ de significância** caso $T_{obs} \geq c_1$ _e_ $T_{obs} \leq c_2$, ou simplesmente se $c_1 \leq T_{obs} \leq c_2$.

Note que formalmente sempre frisamos "ao nível $\alpha\%$ de significância", pois os níveis críticos $c_1$ e $c_2$ serão _sempre_ funções de $\alpha$.

- Quando simulamos a distribuição de $T$ sob $H_0$, $c_1$ e $c_2$ aqui são simplesmente dados pelos percentis $\alpha/2$ e $1 - \alpha/2$ dessa distribuição.
- Analogamente, se utilizarmos alguma teoria ou aproximação, $c_1$ e $c_2$ serão dados pelos mesmos percentis da distribuição correspondente (por exemplo a Binomial).

- Na nomenclatura usual de Testes de Hipóteses, o conjunto de pontos entre $c_1$ e $c_2$ definem a **Região de Aceitação** do teste.
- Formalmente, $RA := \{T_{obs} \!: c_1 \leq T_{obs} \leq c_2\}$.
- Como o próprio nome implica, _aceitamos $H_0$ para todos os valores_ $T_{obs} \in RA$.

- Em contrapartida, o conjunto de pontos menores que $c_1$ e maiores que $c_2$ definem a **Região de Rejeição** (ou _Região Crítica_) do teste.
- Formalmente, $RC := \{T_{obs} \!: T_{obs} < c_1 \,\text{ou}\,\,\, T_{obs} > c_2\}$.
- Como o próprio nome implica, _rejeitamos $H_0$ para todos os valores_ $T_{obs} \in RC$.

- Finalmente, como aqui só existem 2 resultados para um teste de hipóteses (isto é, _ou aceitamos ou rejeitamos_ $H_0$), então $RA$ e $RC$ são _exatamente complementares_, isto é,

\begin{equation*}
    T_{obs} \in RA \Leftrightarrow T_{obs} \notin RC
\end{equation*}

e vice-versa.

### Testes de Hipóteses via Intervalos de Confiança

- Uma outra maneira de interpretar a Região de Aceitação conforme definida acima é como um conjunto de pontos em que os valores observados para uma certa estatística de teste $T$ são _estatisticamente iguais_ sob $H_0$.

- No nosso exemplo, todas as proporções de caras observadas entre $c_1/400 = 180/400 = 0.45$ e $c_2/400 = 220/400 = 0.55$ são _igualmente prováveis_, e consistentes (para essa amostra, de tamanho $n = 400$) com $H_0\!\!: p = 0.50$.
- Essa interpretação é muito similar à de um Intervalo de Confiança!

- De fato, **existe uma relação natural, direta e intrínseca entre os Testes de Hipóteses e Intervalos de Confiança**.
  
> Mais especificamente, lembrando que $\gamma = 1 -\alpha$, um IC de $\gamma\%$ para o parâmetro de interesse **sempre contém todos os valores hipotetizados para o parâmetro de interesse que levariam à aceitação de $H_0$!**.

#### IC exato via Distribuição Binomial

Retornando ao exemplo anterior, temos $T_{obs} = 188$ em $n = 400$ lançamentos da moeda.

Utilizando propriedades da distribuição Binomial, o IC de 95% de confiança _exato_ para o _número de caras_ é dado por


```python
T_obs = 188
n = 400
gamma = 0.95
```


```python
scipy.stats.binom.interval(gamma, n, T_obs/n)
```




    (168.0, 208.0)



Por outro lado, $T_{obs} = 188$ em $n = 400$ lançamentos se traduz em uma proporção de caras estimada igual a $p_{obs} = 0.47$:


```python
p_obs = T_obs/n
p_obs
```




    0.47



Dessa forma, o IC de 95% de confiança análogo para a proporção de caras é então dado por


```python
CI = scipy.stats.binom.interval(gamma, n, p_obs)
left = CI[0]/n
right = CI[1]/n

[left, right]
```




    [0.42, 0.52]



**Nota**: o IC acima para a proporção $p$ é apenas uma _transformação_ do IC para o número de caras, e logo ambos ICs devem sempre levar **às mesmas conclusões**! 

#### IC via simulação

Podemos calcular o IC de 95% de confiança acima também _via simulação_.

Basta proceder como fizemos anteriormente, mas _simulando sob $p_{obs} = 0.47$_:


```python
## Simulando a distribuição de T sob p = 0.47
# ----

# Fixando a semente aleatória (para garantir reproducibilidade)
np.random.seed(42)

## Loop principal
results_CI = np.array([])
for i in np.arange(10000):
    result_CI = np.random.binomial(400, 0.47)
    results_CI = np.append(results_CI, result_CI)

## IC95%
L_CI = np.percentile(results_CI, 2.5)
U_CI = np.percentile(results_CI, 97.5)
```

O IC95% construído por simulação para o número de caras é então dado por:


```python
[L_CI, U_CI]
```




    [168.0, 208.0]



e o IC95% correspondente para a proporção de caras é:


```python
[L_CI/n, U_CI/n]
```




    [0.42, 0.52]



Note que ambos ICs (via distribuição Binomial e via simulação) são _exatamente iguais_! 👍

- Concluímos dessa forma que, embora a proporção de caras observada $p_{obs} = 0.47$ não tenha sido _exatamente_ (ou _numericamente_) igual a $p_0 = 0.50$, podemos afirmar, com 95% de confiança, que $p_{obs}$ é _estatisticamente igual_ a $p_0$, uma vez que ambos os valores estão contidos dentro do IC95%.
- Analogamente, para essa amostra, **qualquer hipótese do tipo $H_0\!\!: p = p_0$ para $p_0 \in [0.42, 0.52]$ seria aceita** ao nível $\alpha = 5\%$!

#### E o bootstrap? 🤔

Ainda uma outra maneira de construir um IC95% via simulação nesse caso é realizando um _bootstrap_ com base na nossa amostra original, `flips_400`:


```python
## Aproximando a distribuição de T via boostrap
# ----

# Fixando a semente aleatória (para garantir reproducibilidade)
np.random.seed(42)

## Loop principal
results_boot = np.array([])
for i in np.arange(10000):
    result_boot = np.sum(flips_400['outcome'].sample(400, replace = True) == "Heads")
    results_boot = np.append(results_boot, result_boot)

## IC95%
L = np.percentile(results_boot, 2.5)
U = np.percentile(results_boot, 97.5)
```


```python
[L, U]
```




    [169.0, 207.0]



Assim como fizemos anteriormente, via bootstrap podemos também aproximar a distribuição amostral da proporção de caras, e dessa forma também calcular seu IC95% análogo:


```python
[L/n, U/n]
```




    [0.4225, 0.5175]



**Importante**: Tanto a construção da distribuição de $T$ sob $H_0$ quanto a construção do IC95% sob $p_{obs} = 0{,}47$ são feitas com base **no mesmo modelo**, isto é, com base na distribuição Binomial.

- Dessa forma, denominamos em geral ambas as técnicas de **paramétricas**, pois pressupõe que os dados vêm de uma distribuição _específica_ (no caso a Binomial) com **parâmetros** específicos (no caso $n = 400$ e $p = 0{,}50$, ou $p = 0{,}47$).
- Dessa forma, como ressaltado acima, **os resultados de ambos os métodos têm que levar às mesmas conclusões**!

Por outro lado, a construção da distribuição amostral empírica de $T$ feita com base no bootstrap 🥾 **não pressupõe** nenhum modelo específico – _utilizamos apenas a amostra_, que nesse caso pode então vir de qualquer população! 

- Dizemos então em geral que o bootstrap é uma técnica **não-paramétrica**, pois não pressupõe praticamente nada sobre a distribuição populacional dos dados.
- Como consequência, as conclusões baseadas nos resultados do boostrap _podem sim ser diferentes_ dos resultados dos métodos acima, **pois as hipóteses sob as quais esses métodos estão baseados são diferentes**!

Em resumo, é importante sempre prestar atenção às hipóteses que estão sendo feitas sobre os dados e métodos, pois somente assim podemos garantir uma coerência entre eles.

- Note inclusive que aqui não conseguimos conduzir um teste de hipóteses "diretamente" através do bootstrap como fizemos acima com base no modelo paramétrico.
    - Lembre que sem um modelo (paramétrico) **bem definido** como base, não conseguimos simular sob $H_0$!

### p-valores

- As regras de aceitação/rejeição de $H_0$ definidas acima são **binárias** e, logo, "rígidas".
- Em outras palavras, _ou aceitamos ou rejeitamos_ $H_0$, e isso ocorre de maneira _independente_ do "quão distante" a estatística de teste $T_{obs}$ se encontra dos pontos críticos $c_1$ e $c_2$!

- Nesse contexto, frequentemente surgem perguntas do tipo: dado que $H_0$ é rejeitada para $T_{obs}$, o quão "extremo" é o valor de $T_{obs}$?
- Em termos precisos, qual a probabilidade de obtermos de um valor de $T_{obs}$ _mais distante_ (na direção de $H_1$) de $c_1$, ou $c_2$?
- A quantificação dessa "distância" em termos de frequência/probabilidade é dada por uma quantidade que denominamos de **p-valor**.

- Mais precisamente, um p-valor $\hat{\alpha}$ é definido como **a probabilidade (sob $H_0$) da nossa estatística de teste $T$ ser menor/maior do que o valor observado $T_{obs}$**, **_na direção em que rejeitamos $H_0$_ (isto é, na direção de $H_1$)**.

No exemplo anterior, como a distribuição de $T$ sob $H_0$ está centrada em $m = 200$ e nossa hipótese nula diz respeito ao fato da moeda ser justa, temos $c_1 = 180$, $c_2 = 220$, e:

- se $T_{obs} < m$, então $\hat{\alpha}$ é dado pela soma da **cauda à esquerda de $T_{obs}$** com a **cauda à direita de $2m - T_{obs}$** da distribuição de $T$ sob $H_0$.
- se $T_{obs} > m$, então $\hat{\alpha}$ é dado pela soma da **cauda à esquerda de $2m - T_{obs}$** com a **cauda à direita de $T_{obs}$** da distribuição de $T$ sob $H_0$.

Para calcular esse valor, basta calcularmos as frequências correspondentes na distribuição de $T$ simulada sob $H_0$:  


```python
T_obs = 188
m = 200
p_lower = np.count_nonzero(results < T_obs) / len(results)
p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
p = p_lower + p_upper
p
```




    0.2148



Naturalmente, como os percentis 2.5 e 97.5 da nossa distribuição simulada para $T$ sob $H_0$ são $c_1 = 180$ e $c_2 = 220$, **qualquer valor de $T_{obs}$ tal que $T_{obs} < c_1$ ou $T_{obs} > c_2$ terá um p-valor menor que $\alpha = 5\%$!**


```python
T_obs = 181
m = 200
p_lower = np.count_nonzero(results < T_obs) / len(results)
p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
p = p_lower + p_upper
p
```




    0.055099999999999996




```python
## Note aqui a maneira de calcular os p-valores é invertida, pois T_obs >= m!.
T_obs = 219
m = 200
p_lower = np.count_nonzero(results < 2*m - T_obs) / len(results)
p_upper = np.count_nonzero(results > T_obs) / len(results)
p = p_lower + p_upper
p
```




    0.055099999999999996




```python
T_obs = 180
m = 200
p_lower = np.count_nonzero(results < T_obs) / len(results)
p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
p = p_lower + p_upper
p
```




    0.0435




```python
T_obs = 220
m = 200
p_lower = np.count_nonzero(results < 2*m - T_obs) / len(results)
p_upper = np.count_nonzero(results > T_obs) / len(results)
p = p_lower + p_upper
p
```




    0.0435



Note que essas probabilidades são muito similares às calculadas exatamente (através da distribuição Binomial) no início dessa aula!

### Exercício ✅

Altere as células de código abaixo para encontrar os valores de $T_{obs}$ necessários para rejeitarmos $H_0$ caso tívessemos escolhido $\alpha = 10\%$ e $\alpha = 20\%$. Preencha também a célula de texto seguinte com o porque você acha razoável que esses valores de $T_{obs}$ sejam maiores/menores do que os valores para rejeitar $H_0$ ao nível de $\alpha = 5\%$, tendo em mente a conexão entre $\alpha$ e o nível de confiança $\gamma$.


```python
# ## Descomente e execute!
# T_obs = ...
# m = 200
# p_lower = np.count_nonzero(results < T_obs) / len(results)
# p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
# p = p_lower + p_upper
# p
```


```python
# ## Descomente e execute!
# T_obs = ...
# m = 200
# p_lower = np.count_nonzero(results < T_obs) / len(results)
# p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
# p = p_lower + p_upper
# p
```

> ...

#### Utilizando os p-valores

- O resultado acima pode ser enunciado em geral da seguinte forma:

> **se $T_{obs} \in RA$, então $\hat{\alpha} \geq \alpha$**.

ou, analogamente,

> **se $T_{obs} \notin RA$, então $\hat{\alpha} \leq \alpha$**.

- Dessa forma, **podemos utilizar o p-valor diretamente para conduzir um teste de hipóteses**, pois
    - se $\hat{\alpha} \geq \alpha$, então $T_{obs} \in RA$ e aceitamos $H_0$;
    - se $\hat{\alpha} < \alpha$, então $T_{obs} \notin RA$ e rejeitamos $H_0$.

Note que a possibilidade de que $p = \alpha$ não ocorre na prática, pois $0 < p < 1$ é um número real.

Tecnicamente, esse é um _evento que ocorre com probabilidade zero_ – você verá mais sobre isso em outros cursos!

- Finalmente, tendo em vista o argumento acima, é comum denominarmos o p-valor $\hat{\alpha}$ de _nível de significância empírico_ ou _observado_, pois **dado $T = T_{obs}$, o p-valor é igual _ao menor valor de $\alpha$ tal que $H_0$ seja rejeitada_**.

#### Controvérsias sobre os p-valores

- Existe [_muita_](https://en.wikipedia.org/wiki/Misuse_of_p-values) controvérsia em torno do uso de p-valores na condução de Testes de Hipóteses, e a principal fonte de controvérsia é dada ao tentar interpretar o p-valor como uma probabilidade.


- Da definição de nível significância observado acima, note que embora _calculemos_ o p-valor como uma probabilidade, $\hat{\alpha}$ depende de um _valor observado_ na amostra, isto é, de $T_{obs}$.

> Dessa forma, o p-valor é na verdade uma **estatística**, e **não uma probabilidade**!

- Embora esse ponto seja bem sutil e será elaborado (repetidamente) de maneira cada vez mais detalhada nos próximos cursos, é importante lembrar que, como _o processo de amostragem é aleatório_, **um valor mais/menos extremo de $T_{obs}$ não significa uma evidência mais/menos forte contra $H_0$**, pois flutuações nos valores de $T$ são _naturalmente esperadas devido à aleatoriedade_.

- Finalmente, embora o p-valor seja muito útil para caracterizar melhor as quantidades envolvidas em um Teste de Hipóteses (e logo dar mais interpretabilidade aos resultados obtidos), **no fim das contas _aceitar ou rejeitar_ $H_0$ é _sempre uma decisão binária_**!

### Conclusões de um Teste de Hipóteses

- Com base no teste de hipótese realizado no exemplo anterior, a diferença de $p_{obs} - p_0 = -0.03$ observada no nosso processo de amostragem pode então, _ao nível de significância de $\alpha = 5\%$_, **ser puramente atribuída ao acaso**!
- Como o teste que construímos é _simétrico_, uma diferença de $p_0 - p_{obs} = 0.03$ também não seria considerada estatisticamente significante.

- Em geral, _qualquer valor_ de $p_{obs}$ no intervalo $RA = [0.45, 0.55]$ que fosse observado para essa amostra de tamanho $n = 400$ seria consistente com a hipótese de que a moeda é justa.
- Analogamente, valores de $p_{obs}$ menores que $0.45$ ou maiores que $0.55$ (ou diferenças maiores que $0.05$ em módulo) seriam considerados estatisticamente significantes e, logo, não seriam consistentes com $H_0$ ao nível de $\alpha = 5\%$ de significância.

- O IC95% complementa a conclusão acima com a informação de que, para essa amostra, _qualquer valor_ $p_0$ no intervalo $[0.42, 0.52]$ seria plausível como a proporção _real_ de caras obtidas em um lançamento dessa moeda.
- Naturalmente, isso inclui o valor $p_0 = 0.50$!

Podemos então também dizer, com 95% de confiança, que a moeda é justa, uma vez que a proporção $p_0 = 0.50$ se encontra dentro do IC95%.

- Finalmente, o p-valor do teste de $\hat{\alpha} = 21.48\%$ nos diz que um resultado como $T_{obs} = 188$ caras, ou uma proporção de $p_{obs} = 0.47$ em $n = 400$ lançamentos de _uma moeda justa_ (isto é, sob $H_0$), **não é tão atípico** assim.

**Não podemos dizer então que esse é um resultado significante**, e logo **não rejeitamos $H_0$**, pois a probabilidade de obtermos um resultado como esse por puro acaso é de 21.48%, o que ao nível $\alpha = 5\%$ é considerado aceitável.

### Exercício ✅

Faça as alterações necessárias nas células de código abaixo para 

1. simular da distribuição de $T$ sob $H_0$,
1. visualizar essa distribuição em um histograma,
1. e calcular o p-valor do teste,

mas agora supondo $n = 40$ e $n = 4.000$.

Suas conclusões são as mesmas para $n = 400$, isto é, aceitamos ou rejeitamos $H_0$ nesses casos? Escreva sua resposta na célula de texto seguinte e comente brevemente sobre o porque você acha que isso acontece.

_<ins> Dica</ins>_: embora o valor de $T_{obs}$ seja diferente para diferentes valores de $n$, estamos supondo aqui que o valor de $p_{obs} = 0{,}47$ continua igual!


```python
# ## Descomente e execute!
# n = ...

# ## Simulando (sob H_0) a distribuição de T
# # ----

# ## Fixando a semente aleatória (para garantir reproducibilidade)
# np.random.seed(42)

# ## Loop principal
# results = np.array([])
# for i in np.arange(10000):
#     result = np.random.binomial(n, 0.5)
#     results = np.append(results, result)
# results
```


```python
# ## Descomente e execute!
# (pd.DataFrame({"results" : results})
#  .plot(kind = 'hist', bins = 20,
#        density = True, ec = 'w', figsize=(10, 5),
#        title=f'Distribuição Empírica do Número de Caras em $n = {n}$ Lançamentos de uma Moeda Justa'));
# plt.axvline(0.47*n, color = 'black', linewidth = 4, label = f'T_obs = {0.47*n}')
# plt.legend()
# plt.ylabel("Densidade");
```


```python
# ## Descomente e execute!
# T_obs = 0.47*n
# m = n/2
# p_lower = np.count_nonzero(results < T_obs) / len(results)
# p_upper = np.count_nonzero(results > 2*m - T_obs) / len(results)
# p = p_lower + p_upper
# p
```

> ...

## Resumo

- Um modelo estatístico pode ser formulado como um conjunto de hipóteses que elaboramos sobre o processo que gerou nossos dados.
- Para verificar a adequação de um modelo estatístico, realizamos um **Teste de Hipóteses**, em que testamos uma hipótese nula $H_0$ contra uma hipótese alternativa $H_1$.
- A **hipótese nula** $H_0$ deve ser um modelo de probabilidade bem definido sobre o processo gerador de dados.
- A **hipótese alternativa** $H_1$ pode ser menos precisa, e usualmente representa o complementar de $H_0$.
- Se aceitamos/rejeitamos $H_0$, então rejeitamos/aceitamos $H_1$, e vice-versa.

Um Teste de Hipóteses é tipicamente conduzido da seguinte maneira:

1. Aproximamos a distribuição de uma **estatística de teste** $T$ sob $H_0$ através de simulação;
1. Analisamos se, nessa distribuição, a ocorrência do valor observado para a nossa estatística de teste $T_{obs}$ por pura chance/aleatoriedade é "alta" ou "baixa", de acordo com algum **nível de significância** $\alpha$ pré-definido.

- Existe uma relação intrínseca entre Testes de Hipóteses com nível de significância $\alpha$ e Intervalos de Confiança com nível de confiança $\gamma = 1- \alpha$.
- Em particular, conclusões tomadas com base em ambas as técnicas **devem ser estritamente as mesmas**.

- Um **p-valor** $\hat{\alpha}$ é uma maneira de quantificar, sob $H_0$, o quão _extremo_ é o valor de $T_{obs}$.
- Podemos formular nossas conclusões em um Teste de Hipóteses apenas com base nos p-valores, mas devemos tomar cuidado ao interpretá-los!
