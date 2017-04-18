# Tutorial 3 - Importando dados no R

Importar dados no R até agora foi algo simples com o auxílio do botão "Import Dataset" do RStudio. Além disso, vimos brevemente a função _read.table_ no primeiro tutorial.

Infelizmente, nenhum dos dois recursos são suficientes para lidar com volume de dados muito grandes, a exemplo dos dados do Programa Bolsa Família retirados do Portal da Transparência e do Cadastro Único.

Há várias maneiras de resolver este problema. Para este curso, ficaremos com as mais simples, porém bastante eficientes. A primeira delas, mostrada no exemplo 1 do início da apostila, é o uso do pacote _data.table_. A segunda, bastante útil para trabalhar com dados já desenhandos e organizado é outras ferramentas como MySQL, MariaDB, PostgreSQL ou BigQuery. No nosso exemplo vamos  é conectar o R a um servidor MySQL, bastante popular para a gestão de bancos de dados relacionais volumosos e usado por diversas empresas e governos. Vejamos as duas soluções.

## Data Table e bases de porte médio

_data.table_ foi um pacote desenvolvido para oferecer uma nova gramática de dados. Nossa escolha foi trabalhar com a gramática oferecida pelo pacote _dplyr_, mas convém examinar duas funções do pacote _data.table_: _fread_ e _fwrite_.

_fread_ é uma alternativa bastante eficiente do uso do botão "Import Dataset" e da função _read.table_ (e sua família). Além de mais rápida, ela identifica a estrutura dos dados para não precisarmos inspecioná-los. Veja como aplicá-la, por exemplo, à versão completa dos dados de saques do Programa Bolsa Família em janeiro de 2017. Veja que você deve inserir o caminho completo do arquivo

```{r}
saques <- fread("C://endereco//no//seu//computador//201701_BolsaFamiliaSacado.csv")
```

Esta solução é adequada para bases de dados grandes, mas que caibam na memória RAM de seu computador. Normalmente, um computador pessoal conseguirá abrir sem problemas bases de alguns poucos Gb.

Da mesma forma que _fread_, _fwrite_ é uma alternativa rápida de exportação de dados. O uso é semelhante ao de _write.table_ ("sep" é o argumento para o separador de colunas):

```{r}
fwrite(valores_munic, "valores_munic.csv", sep = ";")
```

Vale a pena conhecer, em outro momento, como funciona a gramática do pacote _data.table_, mesmo que não tenha funcionalidades diferentes do que vimos com _dplyr_.

## MySQL e dplyr

Uma das soluções mais simples para trabalharmos com bases de dados grandes em R é, em vez de carregar os dados na memória, conectar o R a uma fonte remota de dados e usar a gramática que aprendemos nos tutoriais anteriores para manipularmos os dados. Veremos a seguir um exemplo para dados em um servidor MySQL, mas ele vale para outros sistemas de gerenciamento de dados como PostgreSQL, MariaDB ou BigQuery. Vamos usar no tutorial uma adaptação do exemplo do curso "Data Manipulation in R with dplyr", organizado por Gareth Grolemund, no site Datacamp.

O MySQL não armazena os dados na memória RAM, mas no disco rígido. Isso expande bastante o limite do tamanho dos dados que conseguiremos gerenciar, sem, no entanto, precisar de outra "gramática" para manipulação de dados.

O primeiro passo importante para trabalhar com dados em um servidor MySQL é fazer a conexão com uma base de dados. Vamos supor que temos um banco de dados (que, para o MySQL, significa um conjunto de tabelas, e não apenas uma tabela) chamado "PBF" em um servidor local, ou seja, no próprio computador, e que dentro desse banco de dados existe a tabela "transferencias201701". O usuário e senha fictícias são, respectivamente, "root" e "pass". Usamos, então, a função _src\_mysql_ para criar um objeto de "conexão", que chamaremos de "bd_mysql". Obviamente, o código abaixo não funcionará no seu computador.

```{r}
bd_mysql <- src_mysql(dbname = "PBF", 
                   user = "root",
                   password = "pass")
```

A seguir, criamos um objeto "tabela", que mantém a conexão direto com a tabela "transferencias201701" que está no banco de dados "PBF".

```{r}
tabela <- tbl(base_mysql, "transferencias201701")
```

Simples, não? A partir de agora, basta trabalhar com objeto "tabela" da mesma maneira que trabalhamos _data frames_ até agora. A gramática oferecida pelo pacote _dplyr_ funciona normalmente e, quando quisermos, podemos exportar os dados com os métodos acima.

Vamos ao exemplo do site Datacamp. Os códigos abaixo funcionarão normalmente no seu computador. O site Datacamp mantém um servidor remoto de MySQL que contém um banco de dados "dplyr". Esta, por sua vez, contém uma tabela também chamada "dplyr" (um pouco confuso, pois não precisaria ter o mesmo nome da base de dados) com informações sobre vôos de companhias aéreas.

Comecemos estabelecendo a conexão com o servidor e o banco de dados, tal como fizemos anteriormente. A diferença, agora, é que estamos lidando com um servidor remoto e por isso precisamos informar seu "endereço". Fazemos isso preenchendo os argumentos "host" e "port", cujos valores foram fornecidos pelos criadores do servidor, além, obviamente, do nome do banco de dados, usuário e senha.

```{r}
bd_mysql <- src_mysql(dbname = "dplyr", 
                   host = "courses.csrrinzqubik.us-east-1.rds.amazonaws.com", 
                   port = 3306, 
                   user = "student",
                   password = "datacamp")
```

Uma vez conectados com o servidor, fazemos a conexão direta com a tabela "dplyr", que é a que nos interessa. Vamos chamar o objeto criado de voos. Será este objeto que manipularemos com a gramática que aprendemos anteriormente.

```{r}
voos <- tbl(bd_mysql, "dplyr")
```

Você pode examinar o conteúdo de voos com as funções que já conhecemos: _head_, _str_, etc. Para o nosso exercício, vamos criar a variável "atraso", que é a soma as variáveis atrasos de partidas e chegadas de cada vôo, respectivamente "dep_delay" e "arr_delay". A seguir, vamos agrupar por companhia aérea (variável "carrier"), e calcular a soma agregada dos atrasos das companhias aéras. Tudo isso ser armazenado no objeto "voos_atraso". Note que não há diferença alguma em relação ao que fizemos no passado, exceto o fato de que estamos trabalhando com os dados em um servidor remoto.

```{r}
voos_atraso <- voos %>% 
  mutate(atraso =  dep_delay + arr_delay) %>%
  group_by(carrier) %>%
  summarise(total_atraso = sum(atraso))
```

Quando terminamos de manipular os dados, podemos exportá-los para um arquivo de texto exatamente como fizemos anteriormente. Veja:

```{r}
write.csv(area_atraso, "atraso.csv")
```

## Outras soluções para bases de dados grandes e sparklyr

Há outras soluções para lidarmos com bases de dados grandes. Em geral, elas demandam soluções que incluem o uso de outras ferramentas (Hadoop e Spark, por exemplo), ou o uso de outras linguagens em combinação com o R (C++, por exemplo). Há pacotes de R que implementam tais soluções -- _RHadoop_, _sparklyr_ e _Rcpp_, para as soluções menciondas, respectivamente. A complexidade de tais soluções é bastante mais elevada do que as apresentadas nesta primeira parte do curso e demanda conhecimento avançado em R.

Se o seu problema é apenas volume de dados e manipulação de bases simples, provavelmente as soluções apresentadas neste tutorial são suficientes. 
