Apache Spark é um framework de código aberto para computação e processamento de dados em paralelo ("An unified, open source, parallel, data processing framework for Big Data Analytics") desenvolvido pela Apache Foundation (mesma organização desenvolvedora do Hadoop) focada em análise de dados.

Como _RevoScaleR_ (Tutorial E1), _sparklyr_ é uma alternativa para "escalabilidade", ou seja, para levar a computação em uma única máquina para a computação distribuída em várias.

_sparklyr_ foi é um pacote desenvolvido pela RStudio que tornou o processamento de "big data" em R relativamente mais simples. A grande vantagem do pacote é oferecer a possibilidade manipular dados em Spark com os comandos da biblioteca _dplyr_, exatamente como vimos nos tutoriais anteriores (Tutoriais 2 e 3). Como Spark tem uma API que permite a realização de queries em _SQL_, e vimos no Tutorial B1 que _dplyr_ pode ser usado para queries em SQL, a integração Spark e R via _dplyr_ é facilitada.

Loads data into Spark DataFrames from: local R data frames, Hive tables, CSV, JSON, and Parquet files.

bem como um conjunto de bibliotecas que o tornam capaz de trabalhar de forma integrada, em uma mesma aplicação, com SQL, streaming e análises complexas, para lidar com uma grande variedade de situações de processamento de dados

## Instalação do pacote _sparklyr_ e do Spark

Comecemos instalando o pacote _sparklyr_:

```{r}
install.packages("sparklyr")
```

Você notará após a instalação do pacote que o RStudio reinicirá automaticamente e na janela que contém as abas "Enviroment", "History" e "Git" haverá também uma aba "Spark".

Para fins didáticos e de desenvolvimento, podemos instalar uma versão local do Spark a partir do próprio R com o pacote _sparklyr_:

```{r}
library(sparklyr)
spark_install(version = "1.6.2")
```

## Conexão local ou remota com um cluster Spark

Para ter acesso a um cluster Spark ou à sua versão local temos que criar um objeto de conexão com a função _spark\_connect_. Para uma conexão local fazemos:

```{r}
sc <- spark_connect(master = "local")
```

Você pode também usar o botão "New Connection" na aba Spark para estabelecer a conexão. Para conectar-se a um cluster você deve estar trabalhando em um RStudio Server dentro do cluster ou com uma conexão ao cluster, o que provavelmente não é o caso. Discutiremos esse tópico adiante.

Para testar se a conexão está ativa use:

```{r}
spark_connection_is_open(sc)
```

e para desconectar use:

```{r}
spark_disconnect(sc)
```

## dplyr e Spark

Antes de começar, uma dica: há um [CheatSheet para sparklyr](http://spark.rstudio.com/images/sparklyr-cheatsheet.pdf) da RStudio

A principal vantagem do _sparklyr_ é permitir a manipulação de dados no cluster Spark com os verbos do _dplyr_. Vamos ver exemplos simples com uma conexão a um Spark local para manipular tabelas e produzir análises utilizando os algoritmos de aprendizado de máquina que vimos no Tutorial D1.

Começaremos enviando dados ao cluter. Faremos isso de duas maneiras: abrindo dados na memória para depois enviá-los; e abrindo diretamente no cluster. O pacote _sparklyr_ permite o carregamento de dados em vários formatos, como csv, json e parquet.

Vamos abrir os seguintes dados (todos utilizados no Tutorial D1 sobre aprendizado de máquina):
- Wage: dados sobre salário e característica dos trabalhadores;
- diamonds: dados sobre diamantes e duas características;
- Titanic: dados sobre os passageiros do Titanic provenientes do pacote "titanic" e selecionar as poucas variáveis que nos interessam: Survived, Pclass, Age, Sex e Fare;
- iris: dados sobre flores e suas caraterísticas.

```{r}
# Wage
install.packages("ISLR")
library(ISLR)
data("Wage")

# diamonds
library(ggplot2)
data("diamonds")

# titanic
install.packages("titanic")
library(titanic)
data(titanic_train)

#iris
data(iris)
```

Vamos agora enviá-los ao servidor utilizando a função _copy\_to_:

```{r}
wage_tbl <- copy_to(sc, Wage)
diamonds_tbl <- copy_to(sc, diamonds)
titanic_tbl <- copy_to(sc, titanic_train)
iris_tbl <- copy_to(sc, iris)
```

Note na aba Spark que há 4 com os nomes das tabelas originais. Com _src\_tbls_ podemos ver quais tabelas há no cluster:

```{r}
src_tbls(sc)
```

Os objetos criados (todos com nomes terminados em "tbl") são representações das tabelas em nosso workspace.

Para realizar queries, basta usar tais objetos. Por exemplo, para ficarmos apenas com as variáveis de "titanic_train" que nos interessam utilizamos _select_:

```{r}
titanic <- titanic_tbl %>% 
  select(Survived, Pclass, Age, Sex, Fare)
```

O resultado não é um um data frame na memória de nosso computador, mas a representação de uma tabela temporária gerada no servidor de cluster.

Vamos agora enviar dados ao cluster sem precisar carregá-lo na memória. As funções _spark\_read\_csv_, _spark\_read\_json_ e _spark\_read\_parquet_ cumprem essa tarefa para os formatos csv, json e parquet, respectivamente. Vamos ver um exemplo com _spark\_read\_csv_:

```{r}
download.file("https://raw.githubusercontent.com/leobarone/FLS6397/master/data/fake_data.csv",
              "fake_data.csv")
fake_data_tbl <- spark_read_csv(sc, 
                                name = "fake_data", 
                                path = "fake_data.csv",
                                header = T,
                                delimiter = ";")
```

Para gravar em CSV (ou nos demais formatos) utilizamos a família de funções à qual _spark\_write\_csv pertence:

```{r}
spark_write_csv(iris_tbl, "temp.csv")
```

Como vimos acima de maneira rápida, a manipulação de dados com _dplyr_ é perfeitamente possível num cluster Spark com o pacote _sparklyr_. Alguns exemplos:

- fake_data:

```{r}
fake_data_tbl %>%
  rename(idade = age) %>%
  filter(idade > 18) %>%
  mutate(idade_meses = idade * 12)
```

- iris:

```{r}
iris_tbl %>% 
  group_by(Species) %>%
  summarise(media_tamanho_petala =  mean(Petal_Length),
            media_largura_petala = mean(Petal_Width))
```

- Wage:

```{r}
wage_tbl %>%
  group_by(race) %>%
  summarise(salario_medio = mean(wage),
            desvio_salario = sd(wage))
```

Nada que não tenhamos visto anteriormente e podemos trabalhar no ambiente R integrado com Spark sem precisar aprender outras gramáticas da linguagem.

## Aprendizado de máquina no Spark

Spark é um framework pensado para análise de dados volumosos com velocidade. Por esta razão, aprendizado de máquina é uma das funcionalidades disponíveis em Spark e há diversos algoritmos implementados. O pacote _sparklyr_ oferece funções para os algoritmos e vejamos algumas repetindo dois (não exatamente, mas aproximadamente) do Tutorial D1.

### Regressão linear e previsão de salários e preço de diamantes

Os data frame "Wage" contém dados de salários e características de indivíduos. Nosso objetivo é produzir um modelo linear para prever a variável "wage" (salário) a partir da variável "age" (idade). A função do _sparklyr_ equivalente à _lm_ (regressão linear) é _ml\_linear\_regression. Os dois argumentos principais são o objeto que representa a tabela no cluster -- no nosso caso "wage_tbl" -- e a fórmula.

```{r}
modelo_linear <- ml_linear_regression(wage_tbl, wage ~ age)
summary(modelo_linear)
```

Se quiséssemos separar os dados entre um data frame de treino e outro de teste, bastaria utlizar as funções do _dplyr_. A função sdf\_partition separa os dados em partições que podem ser usadas para treino e teste, como abaixo: 

```{r}
particoes <- sdf_partition(wage_tbl, treino = 0.75, teste = 0.25)
```

Note que o objeto partições contém dois data frames. Para acessar a partição de treino, por exemplo, usamos "particoes$treino". Aplicando o modelo linear a esta partição temos:

```{r}
modelo_linear <- ml_linear_regression(particoes$treino, wage ~ age)
summary(modelo_linear)
```

Finalmente, para armazenar os valores previstos utilizamos _sdf\_predict_. Para reduzir o resultado a apenas os valores observados e previstos, podemos adicionar a função _select_:

```{r}
previsto <- sdf_predict(modelo_linear, particoes$teste) %>% 
  select(wage, prediction)
```

Note que não há nada de muito diferente na produção de um modelo de previsão com regressão linear com as funções do _sparklyr_ em relação ao que podemos fazer com as bibliotecas básicas.

Um outro exemplo da própria documentação do pacote _sparklyr_ pode ser encontrado [aqui](http://spark.rstudio.com/mllib.html#linear_regression).

### Kmeans Cluster e agrupamento de espécies de Flores

Para encerrar, vamos refazer nosso exemplo de Kmeans cluster com as funções do pacote _sparklyr_. Note que não há no pacote algo semelhante ao algoritmo de cluster hierárquico, pois a demanda de memória para gerar uma matriz de distâncias necessária à classificação de muitas observações é demasiada mesmo para um cluster Spark.

Vamos utilizar novamente os dados "iris", uma clássico da literatura de aprendizado de máquina. Para simplificar, vamos utilizar apenas as informações sobre comprimento ("Sepal_Length" e "Petal_Length"):

```{r}
iris2_tbl <- iris_tbl %>%
  select(Sepal_Length, Petal_Length) 
```

A função ml\_kmeans recebe dois argumentos básicos: o objeto que representa o data frame no cluster e o número de grupos que desejamos formar:

```{r}
modelo_kmeans <- ml_kmeans(iris2_tbl, centers = 3)
```

Tal como com modelos lineares, usamos _sdf\_predict_ para produzir um vetor com os grupos aos quais cada observação pertence:

```{r}
grupos <- sdf_predict(modelo_kmeans, iris2_tbl) %>% select(prediction)
```

Tão simples quanto se utilizássemos as biblitecas básicas.

Novamente, uma exemplo com os mesmos dados da documentação do pacote _sparklyr_ pode ser encontrado [aqui](http://spark.rstudio.com/mllib.html#k-means_clustering).

## Mais sobre Spark e computação em nuvem

Há pelo menos duas das grandes empresas de computação em nuvem que se adiantaram para criar produtos que integrem Spark, R e Rstudio. Indico dois tutoriais de como configurar um cluster de Spark+RStudio na nuvem:

- AWS - [RStudio’s R Interface to Spark on Amazon EMR](https://aws.amazon.com/blogs/big-data/running-sparklyr-rstudios-r-interface-to-spark-on-amazon-emr/)
- Cloudera - [Sparklyr (R interface for Spark) and Kerberos on Cloudera](https://medium.com/@bkvarda/sparklyr-r-interface-for-spark-and-kerberos-on-cloudera-80abf5f6b4ad)
