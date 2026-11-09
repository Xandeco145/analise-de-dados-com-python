# Análise de Cancelamento de Clientes

## 1. Sobre o projeto

Este projeto realiza uma análise exploratória de dados (EDA) para investigar o cancelamento de clientes de uma empresa com uma base superior a 800 mil clientes.

O estudo parte de uma base de dados de clientes e procura identificar padrões associados ao cancelamento, observando características como tipo e duração do contrato, plano de assinatura, frequência de uso, contatos com o call center e dias de atraso.

O objetivo é transformar os dados em informações que ajudem a empresa a compreender o comportamento dos clientes e a pensar em ações para reduzir o cancelamento (*churn*).

## 2. Arquivos do projeto

- `inicial.ipynb`: apresenta o contexto do problema e o objetivo do estudo.
- `main.ipynb`: contém o código Python utilizado para carregar, limpar, analisar e visualizar os dados.
- `cancelamentos.csv`: arquivo de dados esperado pelo notebook.

Estrutura esperada:

```text
projeto/
├── inicial.ipynb
├── main.ipynb
└── cancelamentos.csv
```

## 3. Tecnologias utilizadas

- **Python** — linguagem utilizada na análise.
- **Pandas** — leitura, limpeza, manipulação e análise dos dados.
- **Plotly Express** — criação de histogramas interativos.
- **Jupyter Notebook** — ambiente para executar o código em células e visualizar os resultados.

## 4. Dados analisados

O notebook remove a coluna `CustomerID`, que é um identificador do cliente e não é utilizada nas análises. As demais colunas observadas são:

| Coluna | Significado conforme o nome utilizado na base |
|---|---|
| `idade` | Idade do cliente |
| `sexo` | Sexo informado na base |
| `tempo_como_cliente` | Tempo de relacionamento com a empresa |
| `frequencia_uso` | Frequência de utilização do serviço |
| `ligacoes_callcenter` | Quantidade de ligações ao call center |
| `dias_atraso` | Dias de atraso |
| `assinatura` | Categoria do plano: Basic, Premium ou Standard |
| `duracao_contrato` | Duração do contrato: Monthly, Quarterly ou Annual |
| `total_gasto` | Valor total gasto |
| `meses_ultima_interacao` | Meses desde a última interação |
| `cancelou` | Indicador de cancelamento (0 ou 1) |

A interpretação exata das unidades de algumas variáveis depende da documentação da base original, que não foi fornecida.

## 5. Como a análise funciona

O notebook `main.ipynb` executa as seguintes etapas:

### 5.1. Importação e leitura dos dados

O Pandas é importado e o CSV é carregado em um DataFrame:

```python
import pandas as pd

tabela = pd.read_csv("cancelamentos.csv")
tabela = tabela.drop("CustomerID", axis=1)
display(tabela)
```

O arquivo CSV precisa estar no diretório de execução do notebook. Caso esteja em outra pasta, ajuste o caminho informado em `read_csv()`.

### 5.2. Verificação e limpeza

O método `info()` permite verificar a quantidade de registros, as colunas e os valores não nulos. Em seguida, `dropna()` remove linhas que contenham valores ausentes:

```python
display(tabela.info())
tabela = tabela.dropna()
display(tabela.info())
```

Na execução registrada no notebook, a base passou de 50.000 para 49.996 registros após a remoção de valores ausentes.

### 5.3. Análise dos cancelamentos

A coluna `cancelou` é utilizada para contar os clientes que cancelaram e os que permaneceram:

```python
display(tabela["cancelou"].value_counts())

display(
    tabela["cancelou"]
    .value_counts(normalize=True)
    .map("{:.1%}".format)
)
```

`value_counts()` apresenta as quantidades; `normalize=True` apresenta as proporções.

### 5.4. Análise da duração do contrato

O notebook verifica a distribuição dos tipos de contrato e calcula médias das variáveis numéricas agrupadas pela duração:

```python
display(tabela["duracao_contrato"].value_counts(normalize=True))
display(tabela["duracao_contrato"].value_counts())

display(tabela.groupby("duracao_contrato").mean(numeric_only=True))
```

Depois, os contratos mensais são retirados da análise:

```python
tabela = tabela[tabela["duracao_contrato"] != "Monthly"]
```

Essa filtragem altera o próprio DataFrame `tabela` e, portanto, afeta as células executadas posteriormente.

### 5.5. Análise dos planos de assinatura

Os dados são agrupados por plano para observar sua distribuição e as médias das variáveis numéricas:

```python
display(tabela["assinatura"].value_counts(normalize=True))
display(tabela.groupby("assinatura").mean(numeric_only=True))
```

### 5.6. Visualização dos dados

O Plotly Express é utilizado para gerar um histograma para cada coluna, diferenciando os registros pelo indicador de cancelamento:

```python
import plotly.express as px

for coluna in tabela.columns:
    grafico = px.histogram(
        tabela,
        x=coluna,
        color="cancelou",
        width=600
    )
    grafico.show()
```

Os gráficos ajudam a comparar a distribuição das variáveis entre clientes que cancelaram e clientes que não cancelaram.

### 5.7. Aplicação de filtros adicionais

Por fim, são mantidos os registros que atendem simultaneamente aos critérios abaixo:

- Menos de 5 ligações ao call center;
- Até 20 dias de atraso.

```python
tabela = tabela[tabela["ligacoes_callcenter"] < 5]
tabela = tabela[tabela["dias_atraso"] <= 20]

display(tabela)
display(tabela["cancelou"].value_counts())
display(
    tabela["cancelou"]
    .value_counts(normalize=True)
    .map("{:.1%}".format)
)
```

O notebook exibe novamente as quantidades e proporções de cancelamento após a aplicação desses filtros.

## 6. Como instalar e executar

### Pré-requisitos

- Python instalado;
- Jupyter Notebook ou JupyterLab;
- Arquivo `cancelamentos.csv`.

### Instalação

Abra um terminal na pasta do projeto e, opcionalmente, crie um ambiente virtual:

```bash
python -m venv .venv
```

Ative o ambiente:

**Windows (Prompt de Comando):**

```bash
.venv\Scripts\activate
```

**Linux/macOS:**

```bash
source .venv/bin/activate
```

Instale as bibliotecas utilizadas:

```bash
pip install pandas plotly notebook
```

### Execução

Inicie o Jupyter:

```bash
jupyter notebook
```

No navegador, abra `inicial.ipynb` para consultar a apresentação do problema e depois `main.ipynb` para executar a análise.

No `main.ipynb`, execute as células em ordem, de cima para baixo, utilizando **Run All** ou executando cada célula individualmente.

> **Importante:** o notebook modifica o DataFrame `tabela` ao remover valores ausentes e aplicar filtros. Para repetir a análise desde o início, reinicie o kernel e execute todas as células novamente. Isso evita que filtros de uma execução anterior continuem aplicados.

## 7. Resultados e observações

Os resultados registrados nas saídas do notebook mostram análises de cancelamento por duração de contrato e plano de assinatura, além de gráficos para as variáveis da base.

Na última célula registrada, após os filtros de menos de 5 ligações ao call center e até 20 dias de atraso, aparecem 26.267 registros: 21.446 com `cancelou = 0` e 4.821 com `cancelou = 1` (18,4% de cancelamento nesse recorte).

Esses números devem ser interpretados como a saída registrada no notebook, e não como uma conclusão definitiva sobre toda a base. As células contêm saídas salvas de execuções anteriores e algumas contagens exibidas não são consistentes com o total de linhas mostrado em outras etapas. Para validar os resultados, reinicie o kernel e execute o notebook completo com o arquivo CSV correto.

---

**Projeto:** Analisando Dados com Python  
**Tema:** Análise de cancelamento de clientes (*Customer Churn*)
