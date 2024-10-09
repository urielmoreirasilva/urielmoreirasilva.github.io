# Tópico 14 – Intervalos de Confiança [<img src="images/colag_logo.svg" style="float: right; vertical-align: middle; width: 42px; height: 42px;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2014/14%20%E2%80%93%20IntervalosDeConfianca.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 12px; vertical-align: middle; width: 36px; height: 36px;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2014/14%20%E2%80%93%20IntervalosDeConfianca.ipynb)

Nessa aula, vamos aprender mais sobre a quantificação da incerteza sobre uma estimativa através de um conceito chave em Estatística: os Intervalos de Confiança.

### Resultados Esperados

1. Definir o que são Intervalos de Confiança, e aprender a utilizá-los para caracterizar a incerteza sobre uma estimativa.
1. Aprender a interpretar _corretamente_ os Intervalos de Confiança!

### Referências
- [CIT, Capítulo 13](https://inferentialthinking.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)


```python
# Imports para esse tópico.
import numpy as np
import babypandas as bpd
import pandas as pd
import matplotlib.pyplot as plt
plt.style.use('ggplot')

# Opções de como printar objetos do Numpy e do Pandas.
np.set_printoptions(threshold = 20, precision = 2, suppress = True)
pd.set_option("display.max_rows", 7)
pd.set_option("display.max_columns", 8)
pd.set_option("display.precision", 2)
```

### Recapitulando: Salários dos funcionários públicos da cidade de San Diego

Vamos recapitular brevemente o contexto do tópico anterior (Tópico 13): 

- Nossa **população** de interesse consiste nos salários dos funcionários públicos da cidade de San Diego (DataFrame `population`, coluna `'TotalWages'`).
- Para emular uma situação de análise real, "coletamos" uma **amostra** de tamanho $n = 500$ da nossa população (DataFrame `my_sample`, amostrado através do método `.sample`). 
- Queremos estimar a **mediana populacional** dos salários (nosso _parâmetro_) através da **mediana amostral** (nossa _estatística_).


```python
# População.
# Nota: a população aqui está disponível apenas para ilustração dos conceitos! Na prática, não dispomos dessa informação.
population = bpd.read_csv('data/2022_salaries.csv').get(['TotalWages'])
population_median = population.get('TotalWages').median()

# Amostra.
n = 500
np.random.seed(38)
my_sample = population.sample(n)
my_sample
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
      <th>TotalWages</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10301</th>
      <td>27866</td>
    </tr>
    <tr>
      <th>6913</th>
      <td>71861</td>
    </tr>
    <tr>
      <th>5163</th>
      <td>91843</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>3002</th>
      <td>121209</td>
    </tr>
    <tr>
      <th>3718</th>
      <td>109709</td>
    </tr>
    <tr>
      <th>2394</th>
      <td>131409</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 1 columns</p>
</div>




```python
sample_median = my_sample.get('TotalWages').median()
sample_median
```




    76237.0



- Nosso problema fundamental até então era caracterizar a **incerteza** sobre a estimativa fornecida por nossa mediana amostral.
- Com uma estimativa pontual, o máximo que podemos afirmar é que "a mediana populacional é aproximadamente \\$76,237", mas não mais que isso.
- No Tópico 13, aprendemos a utilizar simulação e aproximar a **distribuição amostral** da nossa estatística (mediana amostral) via **bootstrap** para determinar o quanto podemos esperar de variação na mediana amostral de uma amostra para outra, "com uma certa frequência $c\%$".


```python
# Bootstrap!

# Semente aleatória (para garantir reproducibilidade).
np.random.seed(38)

# Inicialização.
n_resamples = 5000
boot_medians = np.array([])

# Loop principal.
for i in np.arange(n_resamples):
    resample = my_sample.sample(500, replace=True)
    median = resample.get('TotalWages').median()
    boot_medians = np.append(boot_medians, median)
    
boot_medians
```




    array([76896. , 72945. , 73555. , ..., 74431. , 75868. , 78601.5])




```python
# "Limite inferior": `x`.
x = np.percentile(boot_medians, 2.5)
x
```




    68469.0




```python
# "Limite superior": `y`.
y = np.percentile(boot_medians, 97.5)
y
```




    81253.5




```python
# Intervalo [x, y].
[x, y]
```




    [68469.0, 81253.5]




```python
bpd.DataFrame().assign(BootstrapMedians = boot_medians).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([x, y], [0, 0], color = 'gold', linewidth = 12, label = '95% percentile interval', zorder = 2);
plt.legend()
plt.ylabel("Densidade");
```


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_10_0.png)
    


- Tomando os percentis 2.5 e 97.5 da distribuição bootstrap, construímos então um **intervalo** que varia de \\$68,469 a \\$81,253.50.  
- Esse intervalo já nós fornece uma maneira de **quantificarmos a incerteza** sobre nossa estimativa original!
- Porém, para que possamos utilizar essa metodologia com segurança na prática, vamos agora formalizar um pouco mais a construção desses intervalos, e definir apropriadamente os objetos envolvidos nesse processo.

## Intervalos de Confiança

- Seja $\gamma \in [0, 1]$ e $\theta$ nosso parâmetro de interesse.
- Um **Intervalo de $\gamma\%$ de Confiança para $\theta$** é um intervalo $[L,\: U]$ que contém, **com $\gamma\%$ de confiança**, o **verdadeiro valor do parâmetro** $\theta$.
- Formalmente, escrevemos $IC_{\gamma\%} (\theta) := [L,\: U]$.

- Apesar da definição acima conter um pouco mais de formalismo matemático do que o realmente necessário, o ponto crucial dos Intervalos de Confiança (IC) é que eles são intervalos cujos valores **dizem respeito ao parâmetro populacional**.
- Mais especificamente, apesar de construírmos os ICs com base em alguma distribuição (como por exemplo a distribuição bootstrap), lembre que essa é uma distribuição dos valores **da estatística**, e **não da amostra**!

No exemplo dos salários, $\theta$ é a mediana populacional dos salários. 

Tomando $\gamma\% = 95\%$, obtivemos $IC_{95\%}(\theta) = [\$68{,}469;\: \$81{,}253.50]$.

Dizemos então que: "_com 95% de confiança_, a mediana **populacional** dos salários está entre \\$68,469 e \\$81,253.50".

### Intervalos de confiança são estimativas _intervalares_ para um parâmetro

- Elaborando mais uma vez sobre o ponto anterior, apesar de construírmos os ICs com base em uma _distribuição_ (no caso a distribuição boostrap), os ICs são **estimativas intervalares** para o nosso parâmetro de interesse.
- Podemos pensar nos ICs como _complementares_ à uma estimativa pontual, nos permitindo então quantificar de maneira apropriada a incerteza sobre essa estimativa.

- Estimativas intervalares são em geral construídas utilizando uma estimativa pontual como _referência_.
- Quando utilizamos bootstrap para construir ICs, os percentis da distribuição bootstrap são naturalmente valores que ocorrem ao "redor da estimativa pontual", uma vez que a **distribuição bootstrap é centrada na estatística** utilizada como estimador para o parâmetro de interesse. 

### Mas afinal, o que quer dizer "confiança" nesse caso?!

- Para responder à essa pergunta, vamos primeiro analisar o experimento abaixo.

#### Capturando o verdadeiro valor do parâmetro

- Suponha que realizemos o seguinte experimento:
    1. Coletamos uma **nova amostra** da população.
    1. Reamostramos dessa nova amostra várias vezes utilizando o bootstrap, calculando a estatística de interesse em cada amostra.
    1. Construímos um $IC_{\gamma\%}$ a partir dos percentis da distribuição bootstrap do passo anterior .

- Um nível $\gamma\%$ de confiança então significa que, **em aproximadamente $\gamma\%$ das vezes em que realizamos esse processo, o intervalo criado conterá o verdadeiro valor do parâmetro**.
    - Em outras palavras: se pudéssemos repetir nosso experimento aleatório um número muito grande de vezes, _$\gamma\%$ dos intervalos de confiança_ vão conter o verdadeiro valor do parâmetro.

#### Múltiplos intervalos de confiança para a mediana dos salários

- Voltemos agora ao nosso exemplo inicial.
- Realizamos o processo acima $M = 200$ vezes, obtendo $M = 200$ ICs diferentes.
- Fizemos isso de antemão (demora um bom tempo!) e salvamos os resultados em um arquivo.
- Os ICs obtidos estão contidos no array `many_cis` abaixo.

Nota: lembre que, em uma situação real, em geral _não conseguimos várias amostras da mesma população, e nem saber o valor real do parâmetro de interesse_ – **dispomos apenas de uma amostra, de uma única estimativa pontual, e logo de um único IC**!


```python
many_cis = np.load('data/many_cis.npy')
many_cis
```




    array([[72881.5 , 85383.32],
           [66727.19, 81871.47],
           [65449.32, 82001.4 ],
           ...,
           [64915.5 , 81814.85],
           [66702.5 , 79711.  ],
           [67996.76, 82105.84]])



- Após realizar as simulações acima, vamos visualizar os resultados!

No gráfico abaixo:

- A <span style="color:blue">linha azul</span> representa a mediana populacional (parâmetro).
- Cada <span style="color:gold">linha dourada</span> representa um dos $M = 200$ IC diferentes, obtidos utilizado o procedimento descrito anteriormente.
- Note que a _maioria_ dos ICs contém o parâmetro verdadeiro, mas _não todos_!


```python
plt.figure(figsize = (10, 6))
for i, ci in enumerate(many_cis):
    plt.plot([ci[0], ci[1]], [i, i], color = 'gold', linewidth = 2)
plt.axvline(x = population_median, color = 'blue');
```


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_29_0.png)
    


#### E quantos ICs não contém o parâmetro populacional? 🤔


```python
plt.figure(figsize = (10, 6))
count_outside = 0
for i, ci in enumerate(many_cis):
    if ci[0] > population_median or ci[1] < population_median:
        plt.plot([ci[0], ci[1]], [i, i], color = 'gold', linewidth = 2)
        count_outside = count_outside + 1
plt.axvline(x = population_median, color = 'blue');
```


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_31_0.png)
    



```python
count_outside
```




    11



- 11 dos nossos $M = 200$ ICs não contém o verdadeiro valor do parâmetro.
- Por outro lado, isso significa que 189/200, ou 94.5% dos ICs, contém sim o parâmetro populacional!
- Não coincidentemente, 94.5% é bem próximo de $\gamma = $ 95%, nosso **nível de confiança**!

- Na prática, como coletamos apenas **uma única amostra**, teremos apenas **um único IC correspondente**, e nunca saberemos _com certeza_ se esse IC contém ou não o verdadeiro valor do parâmetro.
- Ainda assim, saberemos que em $\gamma\%$ dos casos o valor real do parâmetro estará no IC!

### Escolhendo o nível de confiança

- A escolha do nível de confiança $\gamma$ é, na maior parte das vezes, guiada por considerações acerca do problema em questão.
- A literatura e a prática dos especialistas em análise do tipo de problema de interesse com certeza ajudam bastante nesse caso!

- Se, por exemplo, escolhermos um nível de confiança igual a 99%: 
    - aproxidamente apenas 1% das vezes nosso IC não conterá o verdadeiro valor do parâmetro, o que é ótimo!
    - Porém, ...
    - nossos ICs serão **muito largos**, e muitas vezes não serão muito úteis na prática.

No nosso exemplo:


```python
gamma = 0.99
L = np.percentile(boot_medians, 100*(1 - gamma)/2)
U = np.percentile(boot_medians, 100*(1 + gamma)/2)
print(f'Nosso IC{int(100*gamma)}% é dado por:')
print([L, U])

print(f'\nNosso parâmetro é igual a:')
print(population_median)

print('')
bpd.DataFrame().assign(BootstrapMedians = boot_medians).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([L, U], [0, 0], color = 'gold', linewidth = 12, label = f'{int(100*gamma)}% confidence interval', zorder = 2);
plt.scatter(population_median, 0.000004, color = 'blue', s = 100, label = 'population median', zorder = 3)
plt.legend()
plt.ylabel("Densidade");
```

    Nosso IC99% é dado por:
    [66219.0, 84770.0625]
    
    Nosso parâmetro é igual a:
    78136.0
    
    


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_39_1.png)
    


- Se, por outro lado, escolhermos um nível de confiança igual a 80%: 
    - muitas das vezes (~20%) o IC não conterá o verdadeiro valor do parâmetro, o que não é tão bom assim!
    - Porém, ...
    - nossos ICs nesse caso serão **bem mais curtos**, e logo **mais precisos**.

No nosso exemplo:


```python
gamma = 0.80
L = np.percentile(boot_medians, 100*(1 - gamma)/2)
U = np.percentile(boot_medians, 100*(1 + gamma)/2)
print(f'Nosso IC{int(100*gamma)}% é dado por:')
print([L, U])

print(f'\nNosso parâmetro é igual a:')
print(population_median)

print('')
bpd.DataFrame().assign(BootstrapMedians = boot_medians).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([L, U], [0, 0], color = 'gold', linewidth = 12, label = f'{int(100*gamma)}% confidence interval', zorder = 2);
plt.scatter(population_median, 0.000004, color = 'blue', s = 100, label = 'population median', zorder = 3)
plt.legend()
plt.ylabel("Densidade");
```

    Nosso IC80% é dado por:
    [70557.0, 79929.0]
    
    Nosso parâmetro é igual a:
    78136.0
    
    


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_42_1.png)
    


- O _tradeoff_ principal nesse contexto é então entre **confiança e precisão**.
    - Quanto **mais confiante** eu estou de que uma afirmativa é verdadeira, **menos preciso** essa afirmativa será, e vice-versa.
    - Por exemplo, se eu afirmo que hoje vai começar a chover _exatamente_ às 13:14 (uma afirmativa bem precisa), em geral eu quase sempre estarei errado sobre isso (e, logo, pouco confiante).
    - Por outro lado, se eu afirmo que vai chover _algum dia_ desse ano (uma afirmativa pouco precisa), em geral eu quase sempre estarei correto sobre isso (e, logo, muito confiante).   

### Exercício ✅

Modifique `gamma` na célula abaixo até encontrar um intervalo de confiança que _não contenha_ o verdadeiro valor do parâmetro, `population_median`.


```python
gamma = 0.95
L = np.percentile(boot_medians, 100*(1 - gamma)/2)
U = np.percentile(boot_medians, 100*(1 + gamma)/2)
print(f'Nosso IC{int(100*gamma)}% é dado por:')
print([L, U])

print(f'\nNosso parâmetro é igual a:')
print(population_median)

print('')
bpd.DataFrame().assign(BootstrapMedians = boot_medians).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([L, U], [0, 0], color = 'gold', linewidth = 12, label = f'{int(100*gamma)}% confidence interval', zorder = 2);
plt.scatter(population_median, 0.000004, color = 'blue', s = 100, label = 'population median', zorder = 3)
plt.legend()
plt.ylabel("Densidade");
```

    Nosso IC95% é dado por:
    [68469.0, 81253.5]
    
    Nosso parâmetro é igual a:
    78136.0
    
    


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_45_1.png)
    


#### O efeito do tamanho amostral

- Levando em conta a discussão sobre o _tradeoff_ entre precisão e confiança, naturalmente surge a seguinte pergunta: "para um dado nível de confiança $\gamma\%$ **fixo**, como então podemos fazer com que nosso IC seja mais **curto**/preciso?
- Talvez não tão surpreendente, a resposta é: **coletando uma amostra maior**!


```python
## Amostra.
n_e = 2000
np.random.seed(38)
my_sample_e = population.sample(n)
my_sample_e


## Bootstrap!
# Inicialização.
n_resamples_e = 5000
boot_medians_e = np.array([])
# Loop principal.
for i in np.arange(n_resamples_e):
    resample_e = my_sample_e.sample(n_e, replace = True)
    median_e = resample_e.get('TotalWages').median()
    boot_medians_e = np.append(boot_medians_e, median_e)


## IC95%.
gamma_e = 0.95
L_e = np.percentile(boot_medians_e, 100*(1 - gamma_e)/2)
U_e = np.percentile(boot_medians_e, 100*(1 + gamma_e)/2)
print(f'Nosso IC{int(100*gamma_e)}% é dado por:')
print([L_e, U_e])
##
print(f'\nNosso parâmetro é igual a:')
print(population_median)


## Distribuição empírica amostral + IC95%.
print('')
bpd.DataFrame().assign(BootstrapMedians = boot_medians_e).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([L_e, U_e], [0, 0], color = 'gold', linewidth = 12, label = f'{int(100*gamma_e)}% confidence interval', zorder = 2);
plt.scatter(population_median, 0.000004, color = 'blue', s = 100, label = 'population median', zorder = 3)
plt.legend()
plt.ylabel("Densidade");
```

    Nosso IC95% é dado por:
    [71601.0, 79476.0]
    
    Nosso parâmetro é igual a:
    78136.0
    
    


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_48_1.png)
    


### Como _não_ interpretar intervalos de confiança

Fato: intervalos de confiança podem ser complicados de interpretar corretamente.


```python
## Amostra.
n = 500
np.random.seed(38)
my_sample = population.sample(n)
my_sample


## Bootstrap!
# Inicialização.
n_resamples = 5000
boot_medians = np.array([])
# Loop principal.
for i in np.arange(n_resamples):
    resample = my_sample.sample(n, replace=True)
    median = resample.get('TotalWages').median()
    boot_medians = np.append(boot_medians, median)


## IC95%.
gamma = 0.95
L = np.percentile(boot_medians, 100*(1 - gamma)/2)
U = np.percentile(boot_medians, 100*(1 + gamma)/2)
print(f'Nosso IC{int(100*gamma)}% é dado por:')
print([L, U])
##
print(f'\nNosso parâmetro é igual a:')
print(population_median)
```

    Nosso IC95% é dado por:
    [68439.5, 81305.0]
    
    Nosso parâmetro é igual a:
    78136.0
    

**O IC95% contém 95% de todos os salários da população? Não!** ❌


```python
population.plot(kind = 'hist', y = 'TotalWages', bins = np.arange(0, 400000, 10000), density = True, ec = 'w', figsize = (10, 5))
plt.plot([L, U], [0, 0], color = 'gold', linewidth = 12, label = '95% confidence interval');
plt.legend()
plt.ylabel("Densidade");
```


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_52_0.png)
    


Por outro lado, o IC95% _contém sim_ 95% de todos os salários medianos obtidos pelo bootstrap.

> Em outras palavras, o IC95% contém 95% de todos os valores da _distribuição bootstrap_ (mas não da população).


```python
bpd.DataFrame().assign(BootstrapMedians = boot_medians).plot(kind = 'hist', density = True, bins = np.arange(63000, 88000, 1000), ec = 'w', figsize = (10, 5))
plt.plot([L, U], [0, 0], color = 'gold', linewidth = 12, label = '95% confidence interval');
plt.legend()
plt.ylabel("Densidade");
```


    
![png](14%20%E2%80%93%20IntervalosDeConfianca_files/14%20%E2%80%93%20IntervalosDeConfianca_55_0.png)
    


**Então com 95% de probabilidade o IC95% contém o verdadeiro valor do parâmetro populacional? Também não!** ❌

E porque não?
- O parâmetro populacional é **fixo**, isto é, **não-aleatório** (embora seja _desconhecido_).
- Um IC **também é não-aleatório**: embora seu _processo de construção_ seja aleatório (uma vez que o IC é função da amostra, que é aleatória), o IC é _fixo_ para uma _determinada amostra_.

- Para um dado IC, o parâmetro populacional _pertence_ ou _não pertence_ ao IC. Logo, também não há aleatoriedade quanto a esse ponto.

- Finalmente, lembre que o nível de confiança $\gamma\%$ está relacionado ao quão **precisos** estamos sendo ao construirmos um IC.
- Dessa forma, todo o processo de criar o IC (amostragem, bootstrapping e finalmente construir o intervalo) produz um IC que contém o verdadeiro valor do parâmetro em aproximadamente $\gamma\%$ dos casos.

## Resumo

- O boostrap nos fornece uma maneira de construir uma distribuição empírica amostral de uma estatística com base em uma única amostra.
- Com a distribuição bootstrap, podemos criar intervalos de $\gamma\%$ de confiança (IC) tomando como limite inferior e superior os percentis que contenham $\gamma\%$ da distribuição bootstrap.
- Um IC construído dessa maneira nos permite quantificar a incerteza sobre a nossa estimativa do parâmetro populacional.
- Dessa forma, ao invés de reportar apenas uma estimativa pontual para o parâmetro de interesse, podemos reportar um conjunto de estimativas.
- **Intervalos de confiança precisam ser interpretados com cuidado.**
- Nossa "confiança" reside na _consistência_ dos ICs sobre várias amostras e experimentos, e não em um IC em particular para um único experimento.
