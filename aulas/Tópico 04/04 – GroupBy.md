# Tópico 04 – Mais Consultas e GroupBy [<img src="images/colag_logo.svg" style="float: right; vertical-align: middle; width: 42px; height: 42px;">](https://colab.research.google.com/github/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2004/04%20%E2%80%93%20GroupBy.ipynb) [<img src="images/github_logo.svg" style="float: right; margin-right: 12px; vertical-align: middle; width: 36px; height: 36px;">](https://github.com/urielmoreirasilva/urielmoreirasilva.github.io/blob/main/aulas/T%C3%B3pico%2004/04%20%E2%80%93%20GroupBy.ipynb)

Vamos aprender a agrupar e a sumarizar dados!

### Resultados Esperados
1. Aprender a utilizar a função `groupby`.
1. Aprender mais sobre o potencial do `pandas`!

### Referências
- [BPD, Capítulos 10 a 11](https://notes.dsc10.com/)
- [CIT, Capítulo 6](https://inferentialthinking.com/)

Material adaptado do [DSC10 (UCSD)](https://dsc10.com/) por [Flavio Figueiredo (DCC-UFMG)](https://flaviovdf.io/fcd/) e [Uriel Silva (DEST-UFMG)](https://urielmoreirasilva.github.io)


```python
# Importando BabyPandas e Numpy
import babypandas as bpd
import numpy as np
```


```python
# Importando uma nova biblioteca: Matplotlib!
# Utilizaremos essa biblioteca para fazer gráficos
import matplotlib.pyplot as plt
plt.style.use('ggplot')
```

### Sobre os dados: Ainda estamos na feira da Afonso Pena

O DataFrame `afonso_pena` abaixo contém os dados dos feirantes da Afonso Pena:


```python
# Carregando o DataFrame
afonso_pena = bpd.read_csv('data/afonso_pena.csv')

# Calculando a densidade produtos/área de cada barraca
produtos = afonso_pena.get('NUMERO_PRODUTOS_CADASTRADOS')
area = afonso_pena.get('AREA')
afonso_pena = afonso_pena.assign(
    DENSIDADE = produtos / area
)

# Atribuindo um índice a cada uma das linhas
afonso_pena = afonso_pena.set_index('ID_FEIRA_AFONSO_PENA_BARRACA')
afonso_pena
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>84</th>
      <td>G.F3.V052</td>
      <td>BARRACA CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>KARINA RODRIGUES BRANDORFI</td>
      <td>Bijouterias</td>
      <td>BRINCO, ANEL, PULSEIRA, COLAR, ARCO</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>87</th>
      <td>D.F2.V016</td>
      <td>BARRACA CÉLIA APARECIDA DE SOUZA</td>
      <td>CÉLIA APARECIDA DE SOUZA</td>
      <td>EDSON PIRES DE SOUZA</td>
      <td>Vestuário</td>
      <td>BLUSA, BERMUDA, ROUPA DE GINÁSTICA, SAIA, VEST...</td>
      <td>7.0</td>
      <td>11.827592</td>
      <td>0.591836</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1350</th>
      <td>G.F4.V014</td>
      <td>BARRACA HELCIO LICIO SILVA</td>
      <td>HELCIO LICIO SILVA</td>
      <td>GRAZIELA CRISTINA RAMALHO SILVA</td>
      <td>Bijouterias</td>
      <td>ANEL, COLAR, BROCHE, BRINCO, PULSEIRA, ALIANÇA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>1351</th>
      <td>J.F1.V004</td>
      <td>BARRACA DIEGO DOS SANTOS DIAS</td>
      <td>DIEGO DOS SANTOS DIAS</td>
      <td>CELSO DE SOUZA LINHARES</td>
      <td>Calçados</td>
      <td>RASTEIRINHA, SAPATO, SANDÁLIA</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>1352</th>
      <td>D.F4.V050</td>
      <td>BARRACA JAIR CORREA</td>
      <td>JAIR CORREA</td>
      <td>Keli Aparecida Batista Correa</td>
      <td>Vestuário</td>
      <td>VESTIDO DE MALHA, BATA DE TECIDO, CONJUNTO, SA...</td>
      <td>5.0</td>
      <td>11.827592</td>
      <td>0.422740</td>
    </tr>
    <tr>
      <th>1353</th>
      <td>G.F2.V010</td>
      <td>BARRACA SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>ANA PAULA FAUSTINA DE SOUZA</td>
      <td>Bijouterias</td>
      <td>COLAR, GARGANTILHA, ARCO, ANEL, PASSADOR DE CA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>1354</th>
      <td>G.F1.V061</td>
      <td>BARRACA REGINA GARCIA FERREIRA</td>
      <td>REGINA GARCIA FERREIRA</td>
      <td>Rejane Garcia Ferreira Clemente</td>
      <td>Bijouterias</td>
      <td>COLAR DE METAL, PRESILHA, PASSADOR DE CABELO, ...</td>
      <td>4.0</td>
      <td>11.827592</td>
      <td>0.338192</td>
    </tr>
  </tbody>
</table>
<p>1250 rows × 9 columns</p>
</div>



## Qual o número total de barracas de comida? 🍴

### Consultando as _linhas_ de um DataFrame

- **Pergunta:** Como podemos encontrar o _número total_ de linhas de um DataFrame que satisfaçam uma certa condição? 🤔
  
- **Resposta:** Podemos proceder assim:
    1. Realize uma consulta para extrair um DataFrame cujo `'NOME_SETOR'` seja igual a `'Alimentação'`.
    1. Extraia o primeiro elemento `[0]` do método `.shape` aplicado ao DataFrame anterior.


```python
comida = afonso_pena[afonso_pena.get('NOME_SETOR') == 'Alimentação']
comida.shape[0]
```




    100



## Qual o vendedor que vende mais produtos de comida?

## Consultando um _elemento específico_ de um DataFrame

- **Pergunta:** Como podemos encontrar uma _linha específica_ em um DataFrame que satisfaça uma certa condição? 🤔
  
- **Resposta:** Podemos proceder de maneira análoga à pergunta anterior, com alguns passos extras:
    1. Realize uma consulta para extrair um DataFrame cujo `'NOME_SETOR'` seja igual a `'Alimentação'`.
    1. Ordene as linhas do DataFrame resultante por `'NUMERO_PRODUTOS_CADASTRADOS'`, em ordem decrescente.
    1. Extraia o primeiro elemento `[0]` da coluna `'NOME_FEIRANTE'`, utilizando o método `.iloc`.


```python
comida = comida.sort_values(by = 'NUMERO_PRODUTOS_CADASTRADOS', ascending = False)
comida
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>109</th>
      <td>Y.F1.V012</td>
      <td>BARRACA DAYSE PINTO NORBERTO</td>
      <td>DAYSE PINTO NORBERTO</td>
      <td>DJALMA ANTÔNIO DE FREITAS</td>
      <td>Alimentação</td>
      <td>CERVEJA, TORRESMO, CHIPS, AZEITONA, REFRIGERAN...</td>
      <td>21.0</td>
      <td>33.611057</td>
      <td>0.624794</td>
    </tr>
    <tr>
      <th>1406</th>
      <td>Z.F1.V007</td>
      <td>BARRACA FRANCINERE AMARAL CARDOSO RIBEIRO DE S...</td>
      <td>FRANCINERE AMARAL CARDOSO RIBEIRO DE SOUZA</td>
      <td>RAYKARD AGUIAR DE JESUS</td>
      <td>Alimentação</td>
      <td>CERVEJA, REFRIGERANTE, SUCO INDUSTRIALIZADO, E...</td>
      <td>20.0</td>
      <td>33.611058</td>
      <td>0.595042</td>
    </tr>
    <tr>
      <th>1015</th>
      <td>X.F1.V020</td>
      <td>BARRACA VERA LUIZA DE CARVALHO MACEDO</td>
      <td>VERA LUIZA DE CARVALHO MACEDO</td>
      <td>ORLANDINEIA ALVES</td>
      <td>Alimentação</td>
      <td>CERVEJA, SANDUÍCHE NATURAL, BISCOITO, BOLO, RO...</td>
      <td>17.0</td>
      <td>33.595966</td>
      <td>0.506013</td>
    </tr>
    <tr>
      <th>380</th>
      <td>Y.F1.V020</td>
      <td>BARRACA MARGARET LÚCIA DA COSTA SILVA</td>
      <td>MARGARET LÚCIA DA COSTA SILVA</td>
      <td>MARIA TRINDADE DECIOLA DE JESUS</td>
      <td>Alimentação</td>
      <td>ACARAJÉ, BOLINHO DE CARNE DE SOL, SUCO DE AÇAÍ...</td>
      <td>16.0</td>
      <td>33.539371</td>
      <td>0.477051</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Y.F2.V007</td>
      <td>BARRACA ANA CALDEIRA GOMES</td>
      <td>ANA CALDEIRA GOMES</td>
      <td>PAULO HENRIQUE DE JESUS CALDEIRA</td>
      <td>Alimentação</td>
      <td>CHURRASCO, PÃO DE QUEIJO, LEITE, REFRIGERANTE,...</td>
      <td>14.0</td>
      <td>33.611058</td>
      <td>0.416530</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1269</th>
      <td>X.F2.V013</td>
      <td>BARRACA FELIPE RODRIGUES ALVES DE DEUS</td>
      <td>FELIPE RODRIGUES ALVES DE DEUS</td>
      <td>SIDNEY ALVES DE DEUS</td>
      <td>Alimentação</td>
      <td>CHURRASCO</td>
      <td>1.0</td>
      <td>33.558728</td>
      <td>0.029799</td>
    </tr>
    <tr>
      <th>1457</th>
      <td>X.F2.V011</td>
      <td>BARRACA WELLINGTON ALVES DE DEUS</td>
      <td>WELLINGTON ALVES DE DEUS</td>
      <td>SANDRO ALVES DE DEUS</td>
      <td>Alimentação</td>
      <td>CHURRASCO</td>
      <td>1.0</td>
      <td>33.595966</td>
      <td>0.029765</td>
    </tr>
    <tr>
      <th>1423</th>
      <td>X.F1.V003</td>
      <td>BARRACA CELIA APARECIDA MONTEIRO CIPRIANO</td>
      <td>CELIA APARECIDA MONTEIRO CIPRIANO</td>
      <td>APARECIDA SANTOS MONTEIRO</td>
      <td>Alimentação</td>
      <td>ACARAJÉ</td>
      <td>1.0</td>
      <td>33.697508</td>
      <td>0.029676</td>
    </tr>
    <tr>
      <th>261</th>
      <td>X.F2.V003</td>
      <td>BARRACA JOÃO GERALDO MARTINS DE SOUZA</td>
      <td>JOÃO GERALDO MARTINS DE SOUZA</td>
      <td>CHARLES DAVIDSON ROSA MARTINS</td>
      <td>Alimentação</td>
      <td>CHURRASCO</td>
      <td>1.0</td>
      <td>33.569063</td>
      <td>0.029789</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Y.F1.V003</td>
      <td>BARRACA CHAQUIBE HASSAN SOUKI HUNIOR</td>
      <td>CHAQUIBE HASSAN SOUKI HUNIOR</td>
      <td>GISMAR JOSÉ GOMES</td>
      <td>Alimentação</td>
      <td>DOCE DIVERSO</td>
      <td>1.0</td>
      <td>33.605972</td>
      <td>0.029757</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 9 columns</p>
</div>




```python
comida.get('NOME_FEIRANTE').iloc[0]
```




    'DAYSE PINTO NORBERTO'



- O método `.iloc` retorna a n-ésima linha do `DataFrame` (ou `Series`)  correspondente.
- Note que `.iloc` não busca por _índice_, mas sim por _linha_.

Observe que podemos obter o mesmo resultado acima ordenando o DataFrame de maneira _decrescente_ e utilizando o método `.iloc` para obter o _último_ elemento do DataFrame resultante:


```python
comida = comida.sort_values(by = 'NUMERO_PRODUTOS_CADASTRADOS', ascending = True)
comida.get('NOME_FEIRANTE').iloc[-1]
```




    'DAYSE PINTO NORBERTO'



### E se nenhuma condição for satisfeita?

- Quando nenhuma condição da nossa consulta é satisfeita, o resultado ainda é um `DataFrame`, mas com 0 linhas:


```python
afonso_pena[afonso_pena.get('NOME_FEIRANTE') == 'BABY PANDAS']
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
type(afonso_pena[afonso_pena.get('NOME_FEIRANTE') == 'BABY PANDAS'])
```




    babypandas.bpd.DataFrame




```python
afonso_pena[afonso_pena.get('NOME_FEIRANTE') == 'BABY PANDAS'].shape[0]
```




    0



### Exercício ✅

Qual expressão abaixo possui como resultado o número total de **produtos** para crianças?

A. `afonso_pena[afonso_pena.get('NOME_SETOR') == 'Criança'].shape`

B. `afonso_pena[afonso_pena.get('NOME_SETOR') == 'Criança'].get('NUMERO_PRODUTOS_CADASTRADOS').sum()`

C.`afonso_pena[afonso_pena.get('NOME_SETOR') != 'Criança'].get('NUMERO_PRODUTOS_CADASTRADOS').sum()`

D. Mais de um dos itens acima.

### Exercício ✅

**Pergunta**: Quais são os produtos vendidos pelo feirante que mais vende produtos para crianças?

Escreva abaixo uma linha de código que avalie a resposta.


```python
...
```




    Ellipsis



## Quantos feirantes vendem "roupas para crianças" _ou_ "produtos para crianças"?

### Consultas com múltiplas condições

- **Pergunta:** Como podemos filtrar um DataFrame de acordo com múltiplas condições? 🤔
  
- **Resposta:** Basta proceder de maneira análoga ao que fazemos com uma consulta simples, bastando apenas prestar atenção à alguns pequenos detalhes extras:
    - Para escrever uma consulta com múltiplas condições, use `&` para "e" e `|` para "ou".
    - **Você deve sempre usar parênteses `( )` em torno de cada condição!**


```python
afonso_pena[(afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil') | (afonso_pena.get('NOME_SETOR') == 'Criança')]
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>88</th>
      <td>E.F3.V016</td>
      <td>BARRACA CELINA MARIA  SILVA MARCELINO</td>
      <td>CELINA MARIA  SILVA MARCELINO</td>
      <td>ALBERTINO SERGIO MARCELINO</td>
      <td>Vestuário Infantil</td>
      <td>FANTASIA, BERMUDA, CALÇA, SAIA, VESTIDO, CAMISA</td>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.507288</td>
    </tr>
    <tr>
      <th>94</th>
      <td>E.F4.V029</td>
      <td>BARRACA CLARICE CONCEIÇÃO DAS GRAÇAS DE OLIVEI...</td>
      <td>CLARICE CONCEIÇÃO DAS GRAÇAS DE OLIVEIRA LIMA</td>
      <td>ANGELICA MARIA DE OLIVEIRA LIMA</td>
      <td>Vestuário Infantil</td>
      <td>CONJUNTO, SHORT, CUECA, CAMISA, CAMISETA, BLUS...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1426</th>
      <td>F.F3.V010</td>
      <td>BARRACA ALESSANDRA DE ABREU REIS</td>
      <td>ALESSANDRA DE ABREU REIS</td>
      <td>GLAUCIA HELENA DE ABREU TAVARES</td>
      <td>Criança</td>
      <td>ACESSÓRIOS PARA CACHORRO, ALMOFADA, CAMA DE TE...</td>
      <td>14.0</td>
      <td>11.838911</td>
      <td>1.182541</td>
    </tr>
    <tr>
      <th>1435</th>
      <td>E.F4.V006</td>
      <td>BARRACA CILDA LUZIA GUALBERTO</td>
      <td>CILDA LUZIA GUALBERTO</td>
      <td>CILMA MARIA GUALBERTO DE OLIVEIRA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, BOLERO, CUECA, BERMUDA, SAIA, BLUSA</td>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.507288</td>
    </tr>
    <tr>
      <th>1337</th>
      <td>F.F2.V004</td>
      <td>BARRACA MATHEUS PESSALI TIAGO BARBOSA</td>
      <td>MATHEUS PESSALI TIAGO BARBOSA</td>
      <td>MIRNA COSTA GONÇALVES</td>
      <td>Criança</td>
      <td>QUADRO, TOALHA FRALDA, TOALHA, BRINQUEDO PEDAG...</td>
      <td>6.0</td>
      <td>11.838911</td>
      <td>0.506803</td>
    </tr>
    <tr>
      <th>1342</th>
      <td>E.F1.V005</td>
      <td>BARRACA MAIRA FERNANDES DE MOURA</td>
      <td>MAIRA FERNANDES DE MOURA</td>
      <td>MAURÍCIO MARTINS TERRINHA</td>
      <td>Vestuário Infantil</td>
      <td>JARDINEIRA, VESTIDO, SHORT, SAIA, BLUSA</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>1343</th>
      <td>E.F1.V033</td>
      <td>BARRACA ELIANE CORREA JANSEN</td>
      <td>ELIANE CORREA JANSEN</td>
      <td>ANDREA CORREA JANSEN</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, TIARA, BLUSA, BODY, TOUCA, CALCINHA</td>
      <td>6.0</td>
      <td>11.838911</td>
      <td>0.506803</td>
    </tr>
  </tbody>
</table>
<p>224 rows × 9 columns</p>
</div>




```python
# Nota: dentro de parênteses () ou colchetes [], podemos quebrar a linha!
# Isso nos ajuda na leitura do código
afonso_pena[(afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil') | 
   (afonso_pena.get('NOME_SETOR') == 'Criança')]
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>88</th>
      <td>E.F3.V016</td>
      <td>BARRACA CELINA MARIA  SILVA MARCELINO</td>
      <td>CELINA MARIA  SILVA MARCELINO</td>
      <td>ALBERTINO SERGIO MARCELINO</td>
      <td>Vestuário Infantil</td>
      <td>FANTASIA, BERMUDA, CALÇA, SAIA, VESTIDO, CAMISA</td>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.507288</td>
    </tr>
    <tr>
      <th>94</th>
      <td>E.F4.V029</td>
      <td>BARRACA CLARICE CONCEIÇÃO DAS GRAÇAS DE OLIVEI...</td>
      <td>CLARICE CONCEIÇÃO DAS GRAÇAS DE OLIVEIRA LIMA</td>
      <td>ANGELICA MARIA DE OLIVEIRA LIMA</td>
      <td>Vestuário Infantil</td>
      <td>CONJUNTO, SHORT, CUECA, CAMISA, CAMISETA, BLUS...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1426</th>
      <td>F.F3.V010</td>
      <td>BARRACA ALESSANDRA DE ABREU REIS</td>
      <td>ALESSANDRA DE ABREU REIS</td>
      <td>GLAUCIA HELENA DE ABREU TAVARES</td>
      <td>Criança</td>
      <td>ACESSÓRIOS PARA CACHORRO, ALMOFADA, CAMA DE TE...</td>
      <td>14.0</td>
      <td>11.838911</td>
      <td>1.182541</td>
    </tr>
    <tr>
      <th>1435</th>
      <td>E.F4.V006</td>
      <td>BARRACA CILDA LUZIA GUALBERTO</td>
      <td>CILDA LUZIA GUALBERTO</td>
      <td>CILMA MARIA GUALBERTO DE OLIVEIRA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, BOLERO, CUECA, BERMUDA, SAIA, BLUSA</td>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.507288</td>
    </tr>
    <tr>
      <th>1337</th>
      <td>F.F2.V004</td>
      <td>BARRACA MATHEUS PESSALI TIAGO BARBOSA</td>
      <td>MATHEUS PESSALI TIAGO BARBOSA</td>
      <td>MIRNA COSTA GONÇALVES</td>
      <td>Criança</td>
      <td>QUADRO, TOALHA FRALDA, TOALHA, BRINQUEDO PEDAG...</td>
      <td>6.0</td>
      <td>11.838911</td>
      <td>0.506803</td>
    </tr>
    <tr>
      <th>1342</th>
      <td>E.F1.V005</td>
      <td>BARRACA MAIRA FERNANDES DE MOURA</td>
      <td>MAIRA FERNANDES DE MOURA</td>
      <td>MAURÍCIO MARTINS TERRINHA</td>
      <td>Vestuário Infantil</td>
      <td>JARDINEIRA, VESTIDO, SHORT, SAIA, BLUSA</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>1343</th>
      <td>E.F1.V033</td>
      <td>BARRACA ELIANE CORREA JANSEN</td>
      <td>ELIANE CORREA JANSEN</td>
      <td>ANDREA CORREA JANSEN</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, TIARA, BLUSA, BODY, TOUCA, CALCINHA</td>
      <td>6.0</td>
      <td>11.838911</td>
      <td>0.506803</td>
    </tr>
  </tbody>
</table>
<p>224 rows × 9 columns</p>
</div>




```python
afonso_pena[(afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil') | (afonso_pena.get('NOME_SETOR') == 'Criança')].shape[0]
```




    224



⚠️ **Nota**: Não utilize as palavras-chave Python `and` e `or` aqui! Eles não se comportam como você esperaria.


```python
afonso_pena[(afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil') or (afonso_pena.get('NOME_SETOR') == 'Criança')].shape[0]
```




    121



- Uma maneira de verificarmos quantas condições válidas existem para uma certa coluna é utilizar o método `.unique` para verificar quantos valores _únicos_ as linhas dessa coluna assumem:


```python
afonso_pena.get('NOME_SETOR').unique()
```




    array(['Criança', 'Bijouterias', 'Vestuário Infantil', 'Vestuário',
           'Calçados', 'Alimentação', 'Cintos, Bolsas e Acessórios',
           'Artes e Pintura', 'Arranjos e Complementos',
           'Decoração e Utilidades', 'Cama, Mesa, Banho e Tapeçaria',
           'Mobilário, Flores, Arranjos, Cestaria', 'Esculturas'],
          dtype=object)



### Exercício ✅

Cada uma das perguntas a seguir pode ser respondida consultando o DataFrame `afonso_pena`:

1. Quantos feirantes vendem 'Vestuário'?
1. Qual o nome do feirante que mais vende 'Vestuário'?
1. Qual categoria têm mais produtos: 'Artes e Pintura' ou 'Esculturas'?

Tente escrever um código abaixo para responder a cada pergunta.


```python
...
```




    Ellipsis




```python
...
```




    Ellipsis




```python
...
```




    Ellipsis



### Selecionando linhas por _posição_: o método `.take`

- As consultas nos permitem selecionar linhas que satisfaçam uma determinada _condição_.
- Porém, às vezes podemos estar interessados em selecionar linhas _posições_ específicas do DataFrame.
- Fazemos isso com `.take([list_of_integer_positions])`, onde `list_of_integer_positions` são os índices das linhas nas quais estamos interessados.
- O método `.take` nos retorna apenas as linhas cujas posições estão na lista especificada, e logo é análogo a usar `.iloc[]` em uma série.


```python
afonso_pena.take([1, 3, 5])
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>84</th>
      <td>G.F3.V052</td>
      <td>BARRACA CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>KARINA RODRIGUES BRANDORFI</td>
      <td>Bijouterias</td>
      <td>BRINCO, ANEL, PULSEIRA, COLAR, ARCO</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>88</th>
      <td>E.F3.V016</td>
      <td>BARRACA CELINA MARIA  SILVA MARCELINO</td>
      <td>CELINA MARIA  SILVA MARCELINO</td>
      <td>ALBERTINO SERGIO MARCELINO</td>
      <td>Vestuário Infantil</td>
      <td>FANTASIA, BERMUDA, CALÇA, SAIA, VESTIDO, CAMISA</td>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.507288</td>
    </tr>
  </tbody>
</table>
</div>



Podemos também utilizar `.take` junto com intervalos!


```python
afonso_pena.take(np.arange(5))
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>84</th>
      <td>G.F3.V052</td>
      <td>BARRACA CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>KARINA RODRIGUES BRANDORFI</td>
      <td>Bijouterias</td>
      <td>BRINCO, ANEL, PULSEIRA, COLAR, ARCO</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>87</th>
      <td>D.F2.V016</td>
      <td>BARRACA CÉLIA APARECIDA DE SOUZA</td>
      <td>CÉLIA APARECIDA DE SOUZA</td>
      <td>EDSON PIRES DE SOUZA</td>
      <td>Vestuário</td>
      <td>BLUSA, BERMUDA, ROUPA DE GINÁSTICA, SAIA, VEST...</td>
      <td>7.0</td>
      <td>11.827592</td>
      <td>0.591836</td>
    </tr>
  </tbody>
</table>
</div>



## Qual é área total de cada setor?

### Agrupamentos por colunas

- **Pergunta:** Como podemos agrupar um DataFrame de acordo com certas condições? 🤔
  
- **Resposta:** Uma maneira simples e direta é filtrar o DataFrame de acordo com a condição especificada, e depois utilizar alguma função para resumir as informações contidas nas linhas correspondentes (como por exemplo `.count`, `.sum`, `.mean`, `.min`, `.max`, `.median`).

No nosso exemplo, para encontrar a área total do setor correspondente aos produtos de crianças, basta fazer:


```python
afonso_pena[afonso_pena.get('NOME_SETOR') == 'Criança'].get('AREA').sum()
```




    1274.9974161428677



Por outro lado, se quisermos a área total correspondente aos produtos relacionados à vestuário infantil, fazemos:


```python
afonso_pena[afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil'].get('AREA').sum()
```




    1431.87438966735



- Apesar de resolver nosso problema parcialmente, essa abordagem é bem ineficiente!
- Aqui temos que gravar o nome de cada setor, e escrever no mínimo uma linha de código para cada um.
- Se tivermos um grande número de setores, copiar e colar muitas vezes não é muito prático, e geralmente é uma fonte adicional de erros no código.  

### GroupBy: dividir, agregar e combinar

Podemos utilizar o método `.groupby` para responder à nossa pergunta anterior com apenas uma linha de código:


```python
# Descomente as 2 linhas abaixo caso você queira ver todas as linhas do DataFrame
# import pandas as pd
# pd.set_option("display.max_rows", 20)
afonso_pena.groupby('NOME_SETOR').sum()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>733.0</td>
      <td>3359.343283</td>
      <td>21.820242</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>379.0</td>
      <td>615.329088</td>
      <td>32.028438</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>62.0</td>
      <td>713.705731</td>
      <td>2.606116</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>1047.0</td>
      <td>2165.445443</td>
      <td>88.479383</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>774.0</td>
      <td>1881.447403</td>
      <td>65.409968</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>423.0</td>
      <td>1033.677527</td>
      <td>29.927868</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>6.0</td>
      <td>47.580382</td>
      <td>0.252205</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>247.0</td>
      <td>771.488214</td>
      <td>14.012735</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>1454.0</td>
      <td>2591.442504</td>
      <td>122.878561</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>788.0</td>
      <td>1431.874390</td>
      <td>66.590892</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>



Note que os valores na coluna `'AREA'` correspondentes aos setores `'Criança'` e `'Vestuário Infantil'` são exatamente os mesmos calculados acima!

- Em essência, o `.groupby` opera da seguinte maneira:
1. **Divide** as linhas do `DataFrame` de acordo com a coluna especificada (no nosso exemplo, de acordo com o `'NOME_SETOR'`);
1. **Agrega** todas as outras linhas de acordo com a divisão feita, aplicando a função especificada (no nosso exemplo, `.sum`); 
1. **Combina** os resultados obtidos dessa agregação em um novo `DataFrame`, indexado e classificado de acordo com a coluna escolhida (no nosso exemplo, `'NOME_SETOR'`).


```python
afonso_pena.groupby('NOME_SETOR').sum()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>733.0</td>
      <td>3359.343283</td>
      <td>21.820242</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>379.0</td>
      <td>615.329088</td>
      <td>32.028438</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>62.0</td>
      <td>713.705731</td>
      <td>2.606116</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>1047.0</td>
      <td>2165.445443</td>
      <td>88.479383</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>774.0</td>
      <td>1881.447403</td>
      <td>65.409968</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>423.0</td>
      <td>1033.677527</td>
      <td>29.927868</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>6.0</td>
      <td>47.580382</td>
      <td>0.252205</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>247.0</td>
      <td>771.488214</td>
      <td>14.012735</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>1454.0</td>
      <td>2591.442504</td>
      <td>122.878561</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>788.0</td>
      <td>1431.874390</td>
      <td>66.590892</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>




```python
afonso_pena
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>84</th>
      <td>G.F3.V052</td>
      <td>BARRACA CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>KARINA RODRIGUES BRANDORFI</td>
      <td>Bijouterias</td>
      <td>BRINCO, ANEL, PULSEIRA, COLAR, ARCO</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>87</th>
      <td>D.F2.V016</td>
      <td>BARRACA CÉLIA APARECIDA DE SOUZA</td>
      <td>CÉLIA APARECIDA DE SOUZA</td>
      <td>EDSON PIRES DE SOUZA</td>
      <td>Vestuário</td>
      <td>BLUSA, BERMUDA, ROUPA DE GINÁSTICA, SAIA, VEST...</td>
      <td>7.0</td>
      <td>11.827592</td>
      <td>0.591836</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1350</th>
      <td>G.F4.V014</td>
      <td>BARRACA HELCIO LICIO SILVA</td>
      <td>HELCIO LICIO SILVA</td>
      <td>GRAZIELA CRISTINA RAMALHO SILVA</td>
      <td>Bijouterias</td>
      <td>ANEL, COLAR, BROCHE, BRINCO, PULSEIRA, ALIANÇA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>1351</th>
      <td>J.F1.V004</td>
      <td>BARRACA DIEGO DOS SANTOS DIAS</td>
      <td>DIEGO DOS SANTOS DIAS</td>
      <td>CELSO DE SOUZA LINHARES</td>
      <td>Calçados</td>
      <td>RASTEIRINHA, SAPATO, SANDÁLIA</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
    </tr>
    <tr>
      <th>1352</th>
      <td>D.F4.V050</td>
      <td>BARRACA JAIR CORREA</td>
      <td>JAIR CORREA</td>
      <td>Keli Aparecida Batista Correa</td>
      <td>Vestuário</td>
      <td>VESTIDO DE MALHA, BATA DE TECIDO, CONJUNTO, SA...</td>
      <td>5.0</td>
      <td>11.827592</td>
      <td>0.422740</td>
    </tr>
    <tr>
      <th>1353</th>
      <td>G.F2.V010</td>
      <td>BARRACA SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>ANA PAULA FAUSTINA DE SOUZA</td>
      <td>Bijouterias</td>
      <td>COLAR, GARGANTILHA, ARCO, ANEL, PASSADOR DE CA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>1354</th>
      <td>G.F1.V061</td>
      <td>BARRACA REGINA GARCIA FERREIRA</td>
      <td>REGINA GARCIA FERREIRA</td>
      <td>Rejane Garcia Ferreira Clemente</td>
      <td>Bijouterias</td>
      <td>COLAR DE METAL, PRESILHA, PASSADOR DE CABELO, ...</td>
      <td>4.0</td>
      <td>11.827592</td>
      <td>0.338192</td>
    </tr>
  </tbody>
</table>
<p>1250 rows × 9 columns</p>
</div>




```python
afonso_pena.groupby('NOME_SETOR').sum()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>733.0</td>
      <td>3359.343283</td>
      <td>21.820242</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>379.0</td>
      <td>615.329088</td>
      <td>32.028438</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>62.0</td>
      <td>713.705731</td>
      <td>2.606116</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>1047.0</td>
      <td>2165.445443</td>
      <td>88.479383</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>774.0</td>
      <td>1881.447403</td>
      <td>65.409968</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>423.0</td>
      <td>1033.677527</td>
      <td>29.927868</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>6.0</td>
      <td>47.580382</td>
      <td>0.252205</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>247.0</td>
      <td>771.488214</td>
      <td>14.012735</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>1454.0</td>
      <td>2591.442504</td>
      <td>122.878561</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>788.0</td>
      <td>1431.874390</td>
      <td>66.590892</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>



## Caveats do GroupBy

### Colunas desaparecendo! ✨🐇🎩

- O método de agregação que escolhemos para o `.groupby` é aplicado _a cada_ coluna do nosso DataFrame.
- Quando essa operação não fizer sentido para uma determinada coluna, essa coluna desaparecerá!

No nosso exemplo, não podemos somar strings através do método `.sum`, então é por isso que colunas como `'NOME_FANTASIA'` desaparecem automaticamente após realizarmos o `.groupby`.

### Cuidado com a função de agregação escolhida!

- No `.groupby`, a função de agregação é aplicada a cada coluna **separadamente**.
- Dependendo da função utilizada, as linhas do DataFrame resultante precisam ser interpretadas com cuidado!

No nosso exemplo, se quisermos saber a _densidade total_ ocupada por um setor, podemos ficar tentados a simplesmente utilizar um `.groupby` com a função de agregação `.sum`, e olhar para a coluna `'DENSIDADE'` no DataFrame resultante:


```python
afonso_pena.groupby('NOME_SETOR').sum()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>733.0</td>
      <td>3359.343283</td>
      <td>21.820242</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>379.0</td>
      <td>615.329088</td>
      <td>32.028438</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>62.0</td>
      <td>713.705731</td>
      <td>2.606116</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>1047.0</td>
      <td>2165.445443</td>
      <td>88.479383</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>774.0</td>
      <td>1881.447403</td>
      <td>65.409968</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>423.0</td>
      <td>1033.677527</td>
      <td>29.927868</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>6.0</td>
      <td>47.580382</td>
      <td>0.252205</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>247.0</td>
      <td>771.488214</td>
      <td>14.012735</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>1454.0</td>
      <td>2591.442504</td>
      <td>122.878561</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>788.0</td>
      <td>1431.874390</td>
      <td>66.590892</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>



Porém, como a densidade total na verdade seria dada por $\text{densidade total} = \text{número total de produtos}/\text{área total}$, esse resultado não está correto!

Por exemplo, para '`Vestuário Infantil`', a densidade total correta seria igual a:


```python
afonso_pena[afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil'].get('NUMERO_PRODUTOS_CADASTRADOS').sum()/afonso_pena[afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil'].get('AREA').sum()
```




    0.5503276025371656



Por outro lado, a "densidade total" calculada através da soma das densidades no `.groupby` é igual a: 


```python
afonso_pena[afonso_pena.get('NOME_SETOR') == 'Vestuário Infantil'].get('DENSIDADE').sum()
```




    66.59089228766169



## Mais exemplos de usos do GroupBy

### Duas escolhas principais a serem feitas ao usar `.groupby`

- Ao aplicarmos o `.groupby`, devemos sempre ter duas perguntas em mente:
1. Por qual coluna devemos agrupar?
2. Que método de agregação devemos usar?

Veremos abaixo mais algumas aplicações do GroupBy com os dados da Feira da Afonso Pena.

#### Contando a quantidade de linhas de acordo com uma certa condição

- Se quisermos saber o número de linhas de acordo com os valores de uma outra coluna, podemos aplicar o `.groupby` com a função de agregação `.size`.

Por exemplo, se quisermos saber o número de vendedores (lembre que cada linha do nosso DataFrame é um feirante) de acordo com cada setor de vendas, podemos fazer:


```python
afonso_pena.groupby('NOME_SETOR').size()
```




    NOME_SETOR
    Alimentação                              100
    Arranjos e Complementos                   52
    Artes e Pintura                           30
    Bijouterias                              183
    Calçados                                 159
                                            ... 
    Decoração e Utilidades                    69
    Esculturas                                 2
    Mobilário, Flores, Arranjos, Cestaria     42
    Vestuário                                219
    Vestuário Infantil                       121
    Length: 13, dtype: int64



Podemos também utilizar o método `.count` para responder a mesma pergunta acima.

A diferença é que com `.count` a saída em geral é mais completa do que precisamos, mostrando o número de _observações_ disponíveis em cada coluna.

Como no nosso exemplo todas as linhas têm todas as observações completas, com `.count` os mesmos números resultantes de `.size` simplesmente se repetem à cada coluna:


```python
afonso_pena.groupby('NOME_SETOR').count()
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>52</td>
      <td>52</td>
      <td>52</td>
      <td>52</td>
      <td>52</td>
      <td>52</td>
      <td>52</td>
      <td>52</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
      <td>30</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>183</td>
      <td>183</td>
      <td>183</td>
      <td>183</td>
      <td>183</td>
      <td>183</td>
      <td>183</td>
      <td>183</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>159</td>
      <td>159</td>
      <td>159</td>
      <td>159</td>
      <td>159</td>
      <td>159</td>
      <td>159</td>
      <td>159</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>69</td>
      <td>69</td>
      <td>69</td>
      <td>69</td>
      <td>69</td>
      <td>69</td>
      <td>69</td>
      <td>69</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>219</td>
      <td>219</td>
      <td>219</td>
      <td>219</td>
      <td>219</td>
      <td>219</td>
      <td>219</td>
      <td>219</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>121</td>
      <td>121</td>
      <td>121</td>
      <td>121</td>
      <td>121</td>
      <td>121</td>
      <td>121</td>
      <td>121</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 8 columns</p>
</div>



#### Estatísticas descritivas

Podemos também utilizar como funções de agregação as funções de estatísticas descritivas básicas, tais como `.mean`, `.min`, `.max` e `.median`.


```python
afonso_pena.groupby('NOME_SETOR').mean()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>7.330000</td>
      <td>33.593433</td>
      <td>0.218202</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>7.288462</td>
      <td>11.833252</td>
      <td>0.615931</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>2.066667</td>
      <td>23.790191</td>
      <td>0.086871</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>5.721311</td>
      <td>11.833035</td>
      <td>0.483494</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>4.867925</td>
      <td>11.833003</td>
      <td>0.411383</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>6.130435</td>
      <td>14.980834</td>
      <td>0.433737</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>3.000000</td>
      <td>23.790191</td>
      <td>0.126102</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>5.880952</td>
      <td>18.368767</td>
      <td>0.333637</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>6.639269</td>
      <td>11.833071</td>
      <td>0.561089</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>6.512397</td>
      <td>11.833673</td>
      <td>0.550338</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>




```python
afonso_pena.groupby('NOME_SETOR').min()
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>X.F1.V001</td>
      <td>BARRACA ADVALD MARTINS DOS SANTOS</td>
      <td>ADVALD MARTINS DOS SANTOS</td>
      <td>ADAIR DE JESUS FERREIRA BRITO</td>
      <td>ACARAJÉ</td>
      <td>1.0</td>
      <td>33.462435</td>
      <td>0.029676</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>H.F1.V001</td>
      <td>BARRACA ADILMO DE PAIVA VICENTE</td>
      <td>ADILMO DE PAIVA VICENTE</td>
      <td>ADEIZA DE PAIVA VICENTE</td>
      <td>ANEL, ARCO, BRINCO, PASSADOR, COLAR, PULSEIRA</td>
      <td>2.0</td>
      <td>11.827592</td>
      <td>0.168934</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>P.F1.V001</td>
      <td>BARRACA ACIOLI ALVES DE JESUS</td>
      <td>ACIOLI ALVES DE JESUS</td>
      <td>ADELIA GONÇALVES DIAS</td>
      <td>DESENHO, PINTURA ACRÍLICA</td>
      <td>1.0</td>
      <td>23.790191</td>
      <td>0.042034</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>G.F1.V001</td>
      <td>BARRACA ADALGISA COSTA COUTO</td>
      <td>ADALGISA COSTA COUTO</td>
      <td>ADRIANA DELGADO MORAIS ABURACHID</td>
      <td>ANEL, ARCO, PULSEIRA, COLAR, BRINCO</td>
      <td>2.0</td>
      <td>11.827592</td>
      <td>0.168934</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>J.F1.V001</td>
      <td>BARRACA ABÍLIO CESAR FIGUEIREDO FILHO</td>
      <td>ABÍLIO CESAR FIGUEIREDO FILHO</td>
      <td>ABIGAIL BARBOSA MEDEIROS</td>
      <td>BOLSA DE LONA, CHINELO, SANDÁLIA, SAPATILHA</td>
      <td>1.0</td>
      <td>11.827592</td>
      <td>0.084548</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>B.F1.V001</td>
      <td>BARRACA ACYLENE COIMBRA E SILVA</td>
      <td>ACYLENE COIMBRA E SILVA</td>
      <td>ADRIANA CRISTINA SILVA SANTOS</td>
      <td>ABAJOUR, CACHEPOT, LUMINÁRIA</td>
      <td>1.0</td>
      <td>11.827592</td>
      <td>0.084467</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>S.F1.V002</td>
      <td>BARRACA JACQUELINE DIAS DOS SANTOS VICTOR</td>
      <td>JACQUELINE DIAS DOS SANTOS VICTOR</td>
      <td>EULER FERREIRA VICTOR</td>
      <td>ORNAMENTOS DECORATIVOS, ESCULTURA</td>
      <td>2.0</td>
      <td>23.790191</td>
      <td>0.084068</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>A.F1.V001</td>
      <td>BARRACA AGDA DE CÁSSIA MARIA PEREIRA DAIAN</td>
      <td>AGDA DE CÁSSIA MARIA PEREIRA DAIAN</td>
      <td>ADAO SOARES PEREIRA</td>
      <td>ARRANJO DE  FLOR EMBORRACHADA, FLOR DE TECIDO,...</td>
      <td>1.0</td>
      <td>11.827592</td>
      <td>0.047157</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>D.F1.V001</td>
      <td>BARRACA ADALBERTO FERRAZ DE PAULA DANTAS</td>
      <td>ADALBERTO FERRAZ DE PAULA DANTAS</td>
      <td>ADALIA DE SOUZA AMORIM</td>
      <td>BATA, PARCA, CANGA, SAÍDA DE PRAIA, SAIA, BLUS...</td>
      <td>1.0</td>
      <td>11.827592</td>
      <td>0.084467</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>E.F1.V001</td>
      <td>BARRACA ADRIANA PINHEIRO DOS SANTOS</td>
      <td>ADRIANA PINHEIRO DOS SANTOS</td>
      <td>AGDA GIOVANNA VILELA</td>
      <td>BABA OMBRO, CASACO, PAGÃO, CALÇA, TOUCA, SAPAT...</td>
      <td>1.0</td>
      <td>11.827592</td>
      <td>0.084467</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 8 columns</p>
</div>




```python
afonso_pena.groupby('NOME_SETOR').median()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>7.0</td>
      <td>33.595966</td>
      <td>0.208372</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>7.5</td>
      <td>11.833252</td>
      <td>0.633787</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>2.0</td>
      <td>23.790191</td>
      <td>0.084068</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>6.0</td>
      <td>11.827592</td>
      <td>0.506803</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>5.0</td>
      <td>11.827592</td>
      <td>0.422740</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.338192</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>3.0</td>
      <td>23.790191</td>
      <td>0.126102</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>6.0</td>
      <td>21.205709</td>
      <td>0.329097</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>7.0</td>
      <td>11.827592</td>
      <td>0.591271</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>6.0</td>
      <td>11.838911</td>
      <td>0.506803</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 3 columns</p>
</div>




```python
afonso_pena.groupby('NOME_SETOR').max()
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>Z.F4.V004</td>
      <td>BARRACA ZULMA CONCEIÇÃO DE SOUZA</td>
      <td>ZULMA CONCEIÇÃO DE SOUZA</td>
      <td>WELLINGTON RODRIGUES ALVES</td>
      <td>ÁGUA MINERAL, REFRIGERANTE, CHOPP, ÁGUA DE COC...</td>
      <td>21.0</td>
      <td>33.697508</td>
      <td>0.624794</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>H.F2.V032</td>
      <td>BARRACA WAULENO LUIS DE SA</td>
      <td>WAULENO LUIS DE SA</td>
      <td>WANESSA DE FREITAS MARIANO</td>
      <td>TURBANTE, TIARA, FAIXA, PASSADOR, CHAPÉU DE CR...</td>
      <td>12.0</td>
      <td>11.838911</td>
      <td>1.014577</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>P.F2.V011</td>
      <td>BARRACA WILSON BRAGA HENRIQUE</td>
      <td>WILSON BRAGA HENRIQUE</td>
      <td>VALDEMIR NASCIMENTO DOS SANTOS</td>
      <td>QUADRO, PINTURA A ÓLEO</td>
      <td>5.0</td>
      <td>23.790191</td>
      <td>0.210171</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>G.F4.V059</td>
      <td>BARRACA ÍRIS COSTA GUIMARÃES</td>
      <td>ÍRIS COSTA GUIMARÃES</td>
      <td>ÉRICA DE FREITAS LUCENA</td>
      <td>TERÇO, PULSEIRA, BRINCO, MEDALHÃO, BROCHE, ARC...</td>
      <td>13.0</td>
      <td>11.838911</td>
      <td>1.098074</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>J.F4.V046</td>
      <td>BARRACA ZOLMA RODRIGUES LEITE</td>
      <td>ZOLMA RODRIGUES LEITE</td>
      <td>WEBERT CARLOS ALVES</td>
      <td>TÊNIS, SAPATILHA, TÊNIS, RASTEIRINHA, SAPATILHA</td>
      <td>11.0</td>
      <td>11.838911</td>
      <td>0.929140</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>B.F4.V021</td>
      <td>BARRACA ZÉLIA MAGALHÃES DE ANDRADE</td>
      <td>ZÉLIA MAGALHÃES DE ANDRADE</td>
      <td>ZENAIDE SILVA DAS DORES</td>
      <td>ÁRVORE DE PEDRA, INCENSÁRIO, CHAVEIRO, IMAGENS...</td>
      <td>20.0</td>
      <td>21.292159</td>
      <td>1.183673</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>S.F1.V003</td>
      <td>BARRACA JOSÉ RODRIGUES EVANGELISTA</td>
      <td>JOSÉ RODRIGUES EVANGELISTA</td>
      <td>WARLEY PINHEIRO EVANGELISTA</td>
      <td>QUADRO, QUADRO, PLACAS EM MADEIRA, ESCULTURA</td>
      <td>4.0</td>
      <td>23.790191</td>
      <td>0.168137</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>A.F4.V019</td>
      <td>BARRACA WALLACE GROSSI GONÇALVES</td>
      <td>WALLACE GROSSI GONÇALVES</td>
      <td>SONIA MARCIA SOUZA ALEXANDRE</td>
      <td>TAMBORETE, BANCO, PORTA RETRATO, CINZEIRO, CAB...</td>
      <td>16.0</td>
      <td>21.270342</td>
      <td>0.754514</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>D.F4.V062</td>
      <td>BARRACA ZILDA MOREIRA DE OLIVEIRA</td>
      <td>ZILDA MOREIRA DE OLIVEIRA</td>
      <td>ÍSIS DE FREITAS ESPECHIT BRAGA</td>
      <td>VESTIDO, TOUCA, CACHECOL DE TRICÔ, CACHECOL DE...</td>
      <td>12.0</td>
      <td>11.838911</td>
      <td>1.014577</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>E.F4.V033</td>
      <td>BARRACA ÂNGELA ANATOLIA DE OLIVEIRA SALDANHA</td>
      <td>ÂNGELA ANATOLIA DE OLIVEIRA SALDANHA</td>
      <td>WANDA LUCIA DE SOUZA</td>
      <td>VESTIDO, VESTIDO TEMÁTICO, CALCINHA, FOFOQUINH...</td>
      <td>16.0</td>
      <td>11.838911</td>
      <td>1.352769</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 8 columns</p>
</div>



Note que os operadores `.min` e `.max` nesse caso podem ser utilizados para strings, retornando, respectivamente, o _primeiro_ e _último_ elementos de cada coluna em ordem alfabética. 

#### Usando `.sum()` em um array/série booleana

- Um fato muito útil em algumas ocasiões é que a _soma_ de um array/série booleana fornece uma contagem do número de elementos iguais a `True`.
- Isso ocorre porque o Python trata `True` como `1` e `False` como `0`.


```python
True == 1
```




    True




```python
False == 0
```




    True



Suponha que estejamos interessados em encontrar o número total, por setor, dos vendedores com mais de 12 produtos cadastrados.

Primeiramente, criamos um DataFrame com uma coluna a mais ('`MAIS_DE_12`'), refletindo essa condição:


```python
bool_df = afonso_pena.assign(MAIS_DE_12 = afonso_pena.get('NUMERO_PRODUTOS_CADASTRADOS') >= 12)
bool_df
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
      <th>CODIGO_VAGA</th>
      <th>NOME_FANTASIA</th>
      <th>NOME_FEIRANTE</th>
      <th>NOME_PREPOSTO</th>
      <th>NOME_SETOR</th>
      <th>PRODUTOS</th>
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
      <th>MAIS_DE_12</th>
    </tr>
    <tr>
      <th>ID_FEIRA_AFONSO_PENA_BARRACA</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83</th>
      <td>F.F2.V016</td>
      <td>BARRACA CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>CARMEN EMMANUEL DOS SANTOS SILVA</td>
      <td>JANA FONSECA VIEIRA</td>
      <td>Criança</td>
      <td>BOLSA DE BEBÊ, MALA DE MATERNIDADE, NECESSÁIRE...</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
      <td>False</td>
    </tr>
    <tr>
      <th>84</th>
      <td>G.F3.V052</td>
      <td>BARRACA CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>CARMEN FERNANDA ROCHA DE ALCANTARA</td>
      <td>KARINA RODRIGUES BRANDORFI</td>
      <td>Bijouterias</td>
      <td>BRINCO, ANEL, PULSEIRA, COLAR, ARCO</td>
      <td>5.0</td>
      <td>11.838911</td>
      <td>0.422336</td>
      <td>False</td>
    </tr>
    <tr>
      <th>85</th>
      <td>E.F4.V003</td>
      <td>BARRACA CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>CARMEN LÚCIA CARVALHO DE ALMEIDA</td>
      <td>BARBARA ISABELLE CARVALHO DE PAULA</td>
      <td>Vestuário Infantil</td>
      <td>VESTIDO, CONJUNTO, MACACÃO</td>
      <td>3.0</td>
      <td>11.838911</td>
      <td>0.253402</td>
      <td>False</td>
    </tr>
    <tr>
      <th>86</th>
      <td>E.F2.V004</td>
      <td>BARRACA CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>CECÍLIA PAGANO NEVES SALAZAR</td>
      <td>GISELE PAGANO NEVES SALAZAR</td>
      <td>Vestuário Infantil</td>
      <td>MACACÃO, BLUSA, SAPATINHO</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
      <td>False</td>
    </tr>
    <tr>
      <th>87</th>
      <td>D.F2.V016</td>
      <td>BARRACA CÉLIA APARECIDA DE SOUZA</td>
      <td>CÉLIA APARECIDA DE SOUZA</td>
      <td>EDSON PIRES DE SOUZA</td>
      <td>Vestuário</td>
      <td>BLUSA, BERMUDA, ROUPA DE GINÁSTICA, SAIA, VEST...</td>
      <td>7.0</td>
      <td>11.827592</td>
      <td>0.591836</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1350</th>
      <td>G.F4.V014</td>
      <td>BARRACA HELCIO LICIO SILVA</td>
      <td>HELCIO LICIO SILVA</td>
      <td>GRAZIELA CRISTINA RAMALHO SILVA</td>
      <td>Bijouterias</td>
      <td>ANEL, COLAR, BROCHE, BRINCO, PULSEIRA, ALIANÇA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1351</th>
      <td>J.F1.V004</td>
      <td>BARRACA DIEGO DOS SANTOS DIAS</td>
      <td>DIEGO DOS SANTOS DIAS</td>
      <td>CELSO DE SOUZA LINHARES</td>
      <td>Calçados</td>
      <td>RASTEIRINHA, SAPATO, SANDÁLIA</td>
      <td>3.0</td>
      <td>11.827592</td>
      <td>0.253644</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1352</th>
      <td>D.F4.V050</td>
      <td>BARRACA JAIR CORREA</td>
      <td>JAIR CORREA</td>
      <td>Keli Aparecida Batista Correa</td>
      <td>Vestuário</td>
      <td>VESTIDO DE MALHA, BATA DE TECIDO, CONJUNTO, SA...</td>
      <td>5.0</td>
      <td>11.827592</td>
      <td>0.422740</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1353</th>
      <td>G.F2.V010</td>
      <td>BARRACA SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>SIDNEY FERNANDO KNEIPP SOARES</td>
      <td>ANA PAULA FAUSTINA DE SOUZA</td>
      <td>Bijouterias</td>
      <td>COLAR, GARGANTILHA, ARCO, ANEL, PASSADOR DE CA...</td>
      <td>7.0</td>
      <td>11.838911</td>
      <td>0.591271</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1354</th>
      <td>G.F1.V061</td>
      <td>BARRACA REGINA GARCIA FERREIRA</td>
      <td>REGINA GARCIA FERREIRA</td>
      <td>Rejane Garcia Ferreira Clemente</td>
      <td>Bijouterias</td>
      <td>COLAR DE METAL, PRESILHA, PASSADOR DE CABELO, ...</td>
      <td>4.0</td>
      <td>11.827592</td>
      <td>0.338192</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>1250 rows × 10 columns</p>
</div>



Após criar a coluna das booleanas com a nossa condição, é só realizar o GroupBy, agregando com `.sum` por 'NOME_SETOR':


```python
bool_df.groupby('NOME_SETOR').sum()
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
      <th>NUMERO_PRODUTOS_CADASTRADOS</th>
      <th>AREA</th>
      <th>DENSIDADE</th>
      <th>MAIS_DE_12</th>
    </tr>
    <tr>
      <th>NOME_SETOR</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alimentação</th>
      <td>733.0</td>
      <td>3359.343283</td>
      <td>21.820242</td>
      <td>14</td>
    </tr>
    <tr>
      <th>Arranjos e Complementos</th>
      <td>379.0</td>
      <td>615.329088</td>
      <td>32.028438</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Artes e Pintura</th>
      <td>62.0</td>
      <td>713.705731</td>
      <td>2.606116</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Bijouterias</th>
      <td>1047.0</td>
      <td>2165.445443</td>
      <td>88.479383</td>
      <td>1</td>
    </tr>
    <tr>
      <th>Calçados</th>
      <td>774.0</td>
      <td>1881.447403</td>
      <td>65.409968</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Decoração e Utilidades</th>
      <td>423.0</td>
      <td>1033.677527</td>
      <td>29.927868</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Esculturas</th>
      <td>6.0</td>
      <td>47.580382</td>
      <td>0.252205</td>
      <td>0</td>
    </tr>
    <tr>
      <th>Mobilário, Flores, Arranjos, Cestaria</th>
      <td>247.0</td>
      <td>771.488214</td>
      <td>14.012735</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Vestuário</th>
      <td>1454.0</td>
      <td>2591.442504</td>
      <td>122.878561</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Vestuário Infantil</th>
      <td>788.0</td>
      <td>1431.874390</td>
      <td>66.590892</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
<p>13 rows × 4 columns</p>
</div>



Note que os valores contidos na coluna 'MAIS_DE_12' acima são bem diferentes do que temos quando agregamos por 'NOME_SETOR' e simplesmente utilizamos `.size`, como fizemos anteriormente:


```python
bool_df.groupby('NOME_SETOR').size()
```




    NOME_SETOR
    Alimentação                              100
    Arranjos e Complementos                   52
    Artes e Pintura                           30
    Bijouterias                              183
    Calçados                                 159
                                            ... 
    Decoração e Utilidades                    69
    Esculturas                                 2
    Mobilário, Flores, Arranjos, Cestaria     42
    Vestuário                                219
    Vestuário Infantil                       121
    Length: 13, dtype: int64



## Resumo

- Podemos escrever consultas que envolvam múltiplas condições, desde que:
    - Coloquemos parênteses em todas as condições.
    - Separemos as condições utilizando `&` (se você precisar que todas sejam verdadeiras), ou utilizando `|` (se você precisar que pelo menos uma seja verdadeira).
- O método `df.groupby(column_name).agg_method()` nos permite **agregar** todas as linhas de um DataFrame `df` em uma única linha em um novo DataFrame, desde que essas linhas tenham o mesmo valor na coluna `column_name`.
    - As múltiplas linhas são agregadas pelo `.groupby` utilizando um método de agregação `agg_method()` para combinar os diferentes valores.
    - Métodos de agregação comuns incluem `.size`, `.count()`, `.sum()`, `.mean()`, `.median()`, `.max()` e `.min()`.
