# Tópico 09 – Merge [<img src="images/colag_logo.svg" style="float: right; vertical-align: middle; width: 42px; height: 42px;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2009/09%20%E2%80%93%20Merge.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 12px; vertical-align: middle; width: 36px; height: 36px;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2009/09%20%E2%80%93%20Merge.ipynb)

Vamos aprender como juntar DataFrames diferentes!

### Resultados Esperados
1. Aprender um pouco sobre o uso do `merge`.

### Referências
- [BPD, Capítulos 11 e 13](https://notes.dsc10.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)


```python
# Importando BabyPandas, Numpy e o Pandas
import numpy as np
import babypandas as bpd
```

## Merge

<center>
    <img src = "images/FusionDanceFinaleGotenTrunksBuuSaga.webp" width = 45%>    
</center>

### Motivação

Considere uma situação em que temos 2 `DataFrame`s diferentes:
- `telefones`, um DataFrame contendo informações gerais e especificações técnicas acerca de vários modelos de smartphones;
- `unidades`, um DataFrame contendo o número de unidades por loja de eletrônicos da franquia da qual você é dono, em cada um dos shoppings de Belo Horizonte.


```python
# DataFrame 1
telefones = bpd.DataFrame().assign(
    Modelo=['iPhone 13', 'iPhone 13 Pro Max', 'Samsung Galaxy Z Flip', 'Pixel 5a'],
    Preco=[799, 1099, 999, 449],
    Tela=[6.1, 6.7, 6.7, 6.3]
)

# DataFrame 2
unidades = bpd.DataFrame().assign(
    Celular=['iPhone 13 Pro Max', 'iPhone 13', 'Pixel 5a', 'iPhone 13'],
    Unidades=[50, 40, 10, 100],
    Shopping=['Del Rey', 'Savassi', 'Diamond', 'Cidade']
)
```


```python
# DataFrame1
telefones
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
      <th>Modelo</th>
      <th>Preco</th>
      <th>Tela</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13 Pro Max</td>
      <td>1099</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Samsung Galaxy Z Flip</td>
      <td>999</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pixel 5a</td>
      <td>449</td>
      <td>6.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# DataFrame2
unidades
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
      <th>Celular</th>
      <th>Unidades</th>
      <th>Shopping</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13 Pro Max</td>
      <td>50</td>
      <td>Del Rey</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13</td>
      <td>40</td>
      <td>Savassi</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Pixel 5a</td>
      <td>10</td>
      <td>Diamond</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iPhone 13</td>
      <td>100</td>
      <td>Cidade</td>
    </tr>
  </tbody>
</table>
</div>



**Pergunta:** Se você vender todos os telefones do seu estoque (isto é, todas as unidades de todas as lojas), qual será a sua receita total? 🤔

Para responder essa pergunta, primeiramente precisamos _cruzar_ as informações do preço de mercado e da quantidade de cada celular que temos disponíveis em nossas lojas.

Fazemos isso através do método `.merge`!


```python
telefones.merge(unidades, left_on = 'Modelo', right_on = 'Celular')
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
      <th>Modelo</th>
      <th>Preco</th>
      <th>Tela</th>
      <th>Celular</th>
      <th>Unidades</th>
      <th>Shopping</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
      <td>iPhone 13</td>
      <td>40</td>
      <td>Savassi</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
      <td>iPhone 13</td>
      <td>100</td>
      <td>Cidade</td>
    </tr>
    <tr>
      <th>2</th>
      <td>iPhone 13 Pro Max</td>
      <td>1099</td>
      <td>6.7</td>
      <td>iPhone 13 Pro Max</td>
      <td>50</td>
      <td>Del Rey</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pixel 5a</td>
      <td>449</td>
      <td>6.3</td>
      <td>Pixel 5a</td>
      <td>10</td>
      <td>Diamond</td>
    </tr>
  </tbody>
</table>
</div>



Mas o que acabou de acontecer?! 🤯

### O método `.merge`

O método `.merge` funciona da seguinte maneira:
- Escolha um DataFrame "à esquerda" (`left_df`) e outro "à direita" (`right_df`).
- Escolha uma coluna de cada um desses DataFrames, isto é, `left_column_name` e `right_column_name`, respectivamente.
- Execute o merge invocando:
```python
left_df.merge(
    right_df, 
    left_on = left_column_name,
    right_on = right_column_name
)
```

- O DataFrame resultante de um `.merge` contém uma única linha para cada correspondência entre as duas colunas.
- _Usualmente_, os argumentos `left_on` e `right_on` são iguais, mas como no exemplo acima, _isso não é necessário_.
- Note que as linhas em qualquer um dos DataFrames sem correspondência com o outro desaparecem após o merge!

Voltando à nossa pergunta original, para calcular a receita total da venda de todos os aparelhos em todas as lojas, basta somar os produtos entre as colunas `Preco` e `Unidades` após realizarmos o merge:


```python
# Merge: DataFrames 1 e 2, por `Modelo` e `Celular`
merge = telefones.merge(
    unidades,
    left_on = 'Modelo',
    right_on = 'Celular'
)
```


```python
# Calculando a receita total no DataFrame sobre o qual fizemos o _merge_ 
(merge.get('Preco') * merge.get('Unidades')).sum()
```




    171300



Note que a ordem das colunas escolhidas para o merge _não influencia_ o resultado final, apenas a _ordenação_ das colunas do DataFrame resultante.


```python
unidades.merge(telefones, left_on = 'Celular', right_on = 'Modelo')
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
      <th>Celular</th>
      <th>Unidades</th>
      <th>Shopping</th>
      <th>Modelo</th>
      <th>Preco</th>
      <th>Tela</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13 Pro Max</td>
      <td>50</td>
      <td>Del Rey</td>
      <td>iPhone 13 Pro Max</td>
      <td>1099</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13</td>
      <td>40</td>
      <td>Savassi</td>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>iPhone 13</td>
      <td>100</td>
      <td>Cidade</td>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pixel 5a</td>
      <td>10</td>
      <td>Diamond</td>
      <td>Pixel 5a</td>
      <td>449</td>
      <td>6.3</td>
    </tr>
  </tbody>
</table>
</div>



### `.merge` com índices

- O método `.merge` pode ser utilizado com um `DataFrame` _indexado_, e nesse caso toma uma forma ainda mais simples. 
- Nesse caso, em vez de especificarmos ambos `left_on` e `right_on`, especificamos apenas `left_index = True` ou `right_index = True`.


```python
telefones
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
      <th>Modelo</th>
      <th>Preco</th>
      <th>Tela</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13 Pro Max</td>
      <td>1099</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Samsung Galaxy Z Flip</td>
      <td>999</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pixel 5a</td>
      <td>449</td>
      <td>6.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
unidades_com_index = unidades.set_index('Celular')
unidades_com_index
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
      <th>Unidades</th>
      <th>Shopping</th>
    </tr>
    <tr>
      <th>Celular</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>iPhone 13 Pro Max</th>
      <td>50</td>
      <td>Del Rey</td>
    </tr>
    <tr>
      <th>iPhone 13</th>
      <td>40</td>
      <td>Savassi</td>
    </tr>
    <tr>
      <th>Pixel 5a</th>
      <td>10</td>
      <td>Diamond</td>
    </tr>
    <tr>
      <th>iPhone 13</th>
      <td>100</td>
      <td>Cidade</td>
    </tr>
  </tbody>
</table>
</div>




```python
telefones.merge(
    unidades_com_index,
    left_on = 'Modelo',
    right_index = True
)
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
      <th>Modelo</th>
      <th>Preco</th>
      <th>Tela</th>
      <th>Unidades</th>
      <th>Shopping</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
      <td>40</td>
      <td>Savassi</td>
    </tr>
    <tr>
      <th>0</th>
      <td>iPhone 13</td>
      <td>799</td>
      <td>6.1</td>
      <td>100</td>
      <td>Cidade</td>
    </tr>
    <tr>
      <th>1</th>
      <td>iPhone 13 Pro Max</td>
      <td>1099</td>
      <td>6.7</td>
      <td>50</td>
      <td>Del Rey</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pixel 5a</td>
      <td>449</td>
      <td>6.3</td>
      <td>10</td>
      <td>Diamond</td>
    </tr>
  </tbody>
</table>
</div>



Como dito anteriormente, obtemos os mesmos resultados se indexarmos o DataFrame `telefones`, apesar de isso não ser necessário:


```python
telefones_com_index = telefones.set_index('Modelo')
telefones_com_index.merge(
    unidades_com_index,
    left_on = 'Modelo',
    right_index = True
)
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
      <th>Preco</th>
      <th>Tela</th>
      <th>Unidades</th>
      <th>Shopping</th>
    </tr>
    <tr>
      <th>Modelo</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>iPhone 13</th>
      <td>799</td>
      <td>6.1</td>
      <td>40</td>
      <td>Savassi</td>
    </tr>
    <tr>
      <th>iPhone 13</th>
      <td>799</td>
      <td>6.1</td>
      <td>100</td>
      <td>Cidade</td>
    </tr>
    <tr>
      <th>iPhone 13 Pro Max</th>
      <td>1099</td>
      <td>6.7</td>
      <td>50</td>
      <td>Del Rey</td>
    </tr>
    <tr>
      <th>Pixel 5a</th>
      <td>449</td>
      <td>6.3</td>
      <td>10</td>
      <td>Diamond</td>
    </tr>
  </tbody>
</table>
</div>



... e vice-versa com `right_on` e `left_index = True`:


```python
unidades_com_index.merge(
    telefones_com_index,
    right_on = 'Modelo',
    left_index = True
)
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
      <th>Unidades</th>
      <th>Shopping</th>
      <th>Preco</th>
      <th>Tela</th>
    </tr>
    <tr>
      <th>Modelo</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>iPhone 13 Pro Max</th>
      <td>50</td>
      <td>Del Rey</td>
      <td>1099</td>
      <td>6.7</td>
    </tr>
    <tr>
      <th>iPhone 13</th>
      <td>40</td>
      <td>Savassi</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>iPhone 13</th>
      <td>100</td>
      <td>Cidade</td>
      <td>799</td>
      <td>6.1</td>
    </tr>
    <tr>
      <th>Pixel 5a</th>
      <td>10</td>
      <td>Diamond</td>
      <td>449</td>
      <td>6.3</td>
    </tr>
  </tbody>
</table>
</div>



### `.merge` com rótulos iguais

- Finalmente, note que se ambos `left_column_name` e `right_column_name` forem iguais, podemos especificar o `.merge` apenas com a opção `on`.


```python
# Criando uma nova coluna `Modelo` igual a `Celular`
unidades_modelo = unidades.assign(Modelo = unidades.get('Celular'))

# Deletando a coluna `Celular`
unidades_modelo = unidades_modelo.drop(columns = 'Celular')

unidades_modelo
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
      <th>Unidades</th>
      <th>Shopping</th>
      <th>Modelo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>50</td>
      <td>Del Rey</td>
      <td>iPhone 13 Pro Max</td>
    </tr>
    <tr>
      <th>1</th>
      <td>40</td>
      <td>Savassi</td>
      <td>iPhone 13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10</td>
      <td>Diamond</td>
      <td>Pixel 5a</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100</td>
      <td>Cidade</td>
      <td>iPhone 13</td>
    </tr>
  </tbody>
</table>
</div>



### Um outro exemplo: "cidades com clima bom" e "cidades com universidades"

Considere o seguinte exemplo, com 2 DataFrames: um contendo algumas "cidades com clima bom" e outro contendo algumas "cidades com universidades", em diferentes estados dos EUA. 


```python
# Cidades com clima bom
nice_weather_cities = bpd.DataFrame().assign(
    city=['La Jolla', 'San Diego', 'Austin', 'Los Angeles'],
    today_high_temp=['79', '83', '87', '87']
    
)

nice_weather_cities
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
      <th>city</th>
      <th>today_high_temp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>La Jolla</td>
      <td>79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>San Diego</td>
      <td>83</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Austin</td>
      <td>87</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Los Angeles</td>
      <td>87</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Cidades com escolas
schools = bpd.DataFrame().assign(
    name=['UCSD', 'University of Chicago', 'University of San Diego','Johns Hopkins University', 'UT Austin', 'SDSU', 'UCLA'], 
    city=['La Jolla', 'Chicago', 'San Diego', 'Baltimore', 'Austin', 'San Diego', 'Los Angeles'],
    state=['California', 'Illinois', 'California', 'Maryland', 'Texas', 'California', 'California'],
    graduation_rate=[0.87, 0.94, 0.78, 0.92, 0.81, 0.83, 0.91 ]
)

schools
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
      <th>name</th>
      <th>city</th>
      <th>state</th>
      <th>graduation_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>UCSD</td>
      <td>La Jolla</td>
      <td>California</td>
      <td>0.87</td>
    </tr>
    <tr>
      <th>1</th>
      <td>University of Chicago</td>
      <td>Chicago</td>
      <td>Illinois</td>
      <td>0.94</td>
    </tr>
    <tr>
      <th>2</th>
      <td>University of San Diego</td>
      <td>San Diego</td>
      <td>California</td>
      <td>0.78</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Johns Hopkins University</td>
      <td>Baltimore</td>
      <td>Maryland</td>
      <td>0.92</td>
    </tr>
    <tr>
      <th>4</th>
      <td>UT Austin</td>
      <td>Austin</td>
      <td>Texas</td>
      <td>0.81</td>
    </tr>
    <tr>
      <th>5</th>
      <td>SDSU</td>
      <td>San Diego</td>
      <td>California</td>
      <td>0.83</td>
    </tr>
    <tr>
      <th>6</th>
      <td>UCLA</td>
      <td>Los Angeles</td>
      <td>California</td>
      <td>0.91</td>
    </tr>
  </tbody>
</table>
</div>



### Exercício ✅

Apenas olhando as células acima e **sem escrever nenhuma linha de código**, quantas linhas terá o DataFrame `nice_weather_cities.merge(schools, on = 'city')`?

A. 4

B. 5

C. 6

D. 7

E. 8

## Resumo

- Para combinar informações de vários `DataFrame`s, podemos utilizar o método `.merge`.
- Ao usar `.merge`, o Python procura uma correspondência entre uma coluna especificada em cada DataFrame, e combina as linhas onde há correspondência.
- Nos casos em que não houver correspondência, a linha desaparece!
