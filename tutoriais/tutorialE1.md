# Tutorial B1 - RevoScaleR

Neste tutorial vamos ver um breve introdução ao pacote _RevoScaleR_. Infelizmente, _RevoScaleR_ não é um pacote disponível para a distribuiçao open-source do R e para utilizá-la devemos instalar o Microsoft R Client, disponível apenas para Windows.

Nos laboratórios do curso não há instalação do Microsoft R Cliente e faremos apenas uma demonstração de seu uso. Você pode ler este tutorial para aprender um pouco mais sobre _RevoScaleR_, o formato de dados que utiliza e as funções e métodos disponíveis e, se tiver interesse, acompanhá-lo quando tiver uma versão do Microsoft R Client instalada.

## Porque usar o _RevoScaleR_?

Há duas razões pelas quais o _RevoScaleR_ é atrativo no contexto do curso. A primeira delas é, se seu uso do R for estritamente local, o pacote oferece uma estratégia eficiente de superar o problema de dados que extrapolam a memória RAM do computador. Essa limitação é superada pelo uso de um formato próprio de dados cuja extensão é .xdf (External Data Frame), que é armazenado no disco e não na memória RAM de seu computador. Basicamente, _RevoScaleR_ é um pacote que oferece métodos para arquivos em formato .xdf, que vão desde a manipulação básica de dados e apresentação gráfica, até os principais algoritmos de aprendizado de máquina.

A segunda razão é que o Microsoft R Client oferece integração com o SGBD Teradata. A opção da empresa Teradata foi descontinuar a integração de seus produtos com a versão open-source do R e focar no desenvolvimento integrado com o produto da Microsoft. Você pode ler mais sobre o tema e aprender sobre essa integração em [RevoScaleR Teradata Getting Started Guide](https://msdn.microsoft.com/en-us/microsoft-r/scaler-teradata-getting-started). Você também encontra informações adicionais para instalação [aqui](https://msdn.microsoft.com/en-us/microsoft-r/rserver-install-teradata-client). Para usufruir da integração é preciso aprender a utilizar o pacote _RevoScaleR_, o que faremos no presente tutorial.

## Mais uma gramática para dados: _RevoScaleR_

_RevoScaleR_ tem a sua própria gramática de manipulação, apresentação e análise de dados. Essa gramática é exclusiva de dados em formato .xdf.

O primeiro passo para lidar com dados com a gramática do _RevoScaleR_ é transformá-los em formato .xdf. Vamos fazer um exemplo com dados em formato .csv, mas você pode importar dados via ODBC (veja este guia [RevoScaleR ODBC Data Import](https://msdn.microsoft.com/en-us/microsoft-r/scaler-odbc)). Neste outro guia [Data Sources](https://msdn.microsoft.com/en-us/microsoft-r/scaler-user-guide-data-source) você encontra mais informações sobre os métodos disponíveis para importação de dados, que inclui SPSS e Teradata.

Faremos um exemplo simples com dados gerados com simulação apra fins didáticos ("fake\_data"), disponíveis [aqui](https://raw.githubusercontent.com/leobarone/mq_bsb_17/master/dados/fake_data.csv). Vamos começar criando dois vetores atômicos de texto (vulgo, um objeto que só contém um texto), um com o endereço do arquivo .csv existente e outro com o endereço deseja para o futuro arquivo .xdf:

```{r}
arquivo_csv <- "~/mq_bsb_17/dados/fake_data.csv"
arquivo_xdf <- "~/mq_bsb_17/dados/fake_data.xdf"
```

A seguir, vamos utilizar a função _rxImport_ para gerar o arquivo .xdf. Apesar do nome, a função não carrega os dados na memória RAM. Há diversos argumentos para a função, mas, basicamente, precisamos apenas informar os dados que serão transformados e o arquivo .xdf que será gerado:

```{r}
rxImport(inData = arquivo_csv,
         outFile = arquivo_xdf)
```

Se o resultado de rxImport for atribuído a um objeto, como no exemplo abaixo, teremos uma representação do data frame externo em nosso workspace (algo semelhante ao que fizemos com dados de SGBD como o MySQL, mas tendo como local dos dados o disco):

```{r}
fake_data <- rxImport(inData = arquivo_csv,
                      outFile = arquivo_xdf)
```

Para obter informações rápidas sobre o data frame externo usamos a função _rxGetInfo_

```{r}
rxGetInfo(fake_data, getVarInfo = TRUE)
```

Se adicionarmos o argumento "numRows", também teremos uma visão rápida das primeiras linhas:

```{r}
rxGetInfo(fake_data, getVarInfo = TRUE, numRows = 6)
```

Outra função útil para conhecer os dados, semelhante à função _summary_ da biblioteca básica do R, é _rxSummary_ (você já deve ter notado que todas as funções do _RevoScaleR_ começam com "rx"). O detalhe, comum às demais funções do pacote, é que precisamos especificar uma fórmula (mesmo não havendo nenhum modelo sendo contruído). As fórmulas funcionam da mesma maneira que nos demais pacotes de R e têm a seguinte estrutura: "varivel_dependente ~ variavel_independente1 + variavel_independente2 + ...". Quando não há "variável dependente", como é o caso de um sumário de dados, basta omitir o lado esquerdo da fórmula e começar a partir do símbolo "~"

```{r}
rxSummary(formula = ~ income + savings + age, 
          data = fake_data)
```

Podemos produzir um histograma com _rxHistogram_, que utiliza os mesmos argumentos de _rxSummary_:

```{r}
rxHistogram(formula = ~age, 
            data = fake_data)
```

A transformação de variáveis com _RevoScaleR_ é feita basicamente utilizando a função _RxDataStep_. Esta função guarda bastante semelhança com _rxImport_ por ter como argumentos principas "inData" e "outFile". Este último, porém, não precisa ser especificado se o objetivo for sobrescrever os dados originais com os dados tranformados (que não é o nosso caso).

Três argumentos das funções permitem as transformações essenciais dos dados. "varsToKeep", se preenchido com um vetor de nome das variáveis, seleciona as variáveis que permanecerão no data frame resultantes. "rowSelection" permite fazer a seleção de linhas. Finalmente, "transforms" recebe uma lista de operações de variáveis a serem realizadas.

No exemplo abaixo, manteremos apenas as variáveis "age", "income" e "savings", selecionaremos as linhas que têm valor maior ou igual a 25 na variável "age" e produziremos três transformações a partir das variáveis originais: 

```{r}
arquivo2_xdf <- "~/mq_bsb_17/dados/fake_data2.xdf"
rxDataStep(inData = arquivo_xdf, 
         varsToKeep = c( "age", "income", "savings"),
         rowSelection = age > 25
	       transforms = list(prop_income_sav = income / savings,
	                         savings_age = savings/ age,
	                         age_months = age))
```

Para transformar os dados que estão em .xdf em data frame no R basta usar _rxXdfToDataFrame_, que permite, como _rxDataStep_, seleção de linhas, colunas e operação de variáveis:

```{r}
fake_data <- rxXdfToDataFrame(inData = arquivo_xdf,
         varsToKeep = c( "age", "income", "savings"),
         rowSelection = age > 25
	       transforms = list(prop_income_sav = income / savings,
	                         savings_age = savings/ age,
	                         age_months = age))
```

Para situações em que precisamos combinar mais de um data frame, podemos usar _rxMerge_. Esta função recebe dois data frames externos ("inData1" e "inData2") e cria uma combinação segundo um "type" -- que pode ser "inner", "full", "left" e "right" -- a partir de uma ou mais chaves ("matchVars") e criando um novo arquivo .xdf ("outFile").

Vejamos como ficaria o exemplo de "inner join" que fizemos no Tutorial 3 se utilizarmos a função _rxMerge_:

```{r}
download.file("https://raw.githubusercontent.com/leobarone/FLS6397/master/data/pagamentos11.csv",
              "pagamentos11.csv")
download.file("https://raw.githubusercontent.com/leobarone/FLS6397/master/data/pagamentos17.csv",
              "pagamentos17.csv")

pagamentos11 <- rxImport(inData = "pagamentos11.csv",
                      outFile = "pagamentos11.xdf")
pagamentos17 <- rxImport(inData = "pagamentos17.csv",
                      outFile = "pagamentos17.xdf")

rxMerge(inData1 = "pagamentos11.xdf", 
        inData2 = "pagamentos17.xdf",
        outFile = "pagamentos.xdf", 
        matchVars = "NIS",
        type = "inner")
```

Há outras várias funções de manipulação de dados do _RevoScaleR_. Você encontrará uma lista de funções disponíveis no pacote _RevoScaleR_ [aqui](https://msdn.microsoft.com/en-us/microsoft-r/scaler/scaler)

## _RevoScaleR_ para análise de dados e aprendizado de máquina

Ademais das funções de importação e manipulação de dados, o pacote _RevoScaleR_ oferece diversas funções para análise e aprendizado de máquina.

Voltando a nossos dados originais, podemos realizar cruzamento de dados com _rxCrossTabs_ e _rxCube_:

```{r}
rxCrossTabs(formula = sex ~ educ, data = fake_data)
rxCube(sex ~ educ, data = fake_data)
```

Calcular uma matriz de correlações, novamente usando o argumento "formula":

```{r}
rxCor(formula = ~ age + income + savings,
      data = fake_data)
```

Ou produzir um modelo linear com _rxLinMod_:

```{r}
modelo_linear <- rxLinMod(formula = income ~ savings, 
         data = fake_data)
summary(modelo_linear)
```

Para reproduzir integralmente os exemplos de aprendizado de máquina como o pacote _RevoScaleR_ utilize as funções _rxPredict_, para obter valores previstos, _rxDTree_, para árvores de decisão e _rxKmeans_ para Kmeans Cluster. Dê uma olhada novamente na [lista de funções de Analysis, Learning, and Prediction Functions for Statistical Modeling](https://msdn.microsoft.com/en-us/microsoft-r/scaler/scaler) do _RevoScaleR_ para mais.

## Onde aprender mais

Há um [curso gratuito](https://www.datacamp.com/community/open-courses/big-data-revolution-r-enterprise-tutorial) no site Datacamp criado pela prórpia Revolution Analytics, antes mesmo da empresa ter sido comprada pela Microsoft. Além disso, há uma [introdução](https://msdn.microsoft.com/en-us/microsoft-r/scaler-getting-started) no site da Microsoft bastante útil. Finalmente, você pode ler o [RevoScaleR User's Guider](https://packages.revolutionanalytics.com/doc/8.0.0/win/RevoScaleR_Users_Guide.pdf).

Finalmente, _RevoScaleR_ tem suporte para computação em diversos contextos -- Hadoop, Spark, Teradata e SQL, por exemplo. Para trabalhar localmente com mais de um "core" do processador, é possível utilizar a função _RxLocalParallel_ e _rxSetComputeContext_. Para um guia completo sobre "parallel computaing" com  _RevoScaleR_ veja: [RevoScaleR Distributed Computing Guide](https://packages.revolutionanalytics.com/doc/8.0.0/win/RevoScaleR_Distributed_Computing.pdf)

