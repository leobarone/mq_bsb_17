
## Queries em MySQL

Em primeiro lugar, chamamo o pacote _RMySQL_ e fazemos a conexão ao servidor remoto com _dbConnect_:

```{r}
library(RMySQL)
conexao <- dbConnect(MySQL(), 
                 dbname = "tweater", 
                 host = "courses.csrrinzqubik.us-east-1.rds.amazonaws.com", 
                 port = 3306,
                 user = "student",
                 password = "datacamp")
```

Veja que já escolhemos no argumento _dbname_ o _schema_ ao qual estaremos conectados.

Podemos obter todas as tabelas da base de dados com _dbListTables_:

```{r}
dbListTables(conexao)
```

Para importar uma tabela já existente usamos _dbReadTable_. Por exemplo, vamos importar a tabela _comments_:

```{r}
tabela_importada <- dbReadTable(conexao, "comments")
head(tabela_importada)
```

Importar é custoso demais se as tabela forem muito grandes -- ou impossível se forem maiores do que a memória RAM do seu computador. Se o objetivo for importar "chuncks" da tabela ou importar uma _query_ específica, usamos _dbGetQuery_

```{r}
tabela_query <- dbGetQuery(conexao, "SELECT * FROM comments WHERE user_id = 7")
tabela_query
```

Como é possível observar no código acima, é preciso saber programar em SQL. Vamos à alternativa que não precisa da linguagem SQL para gerar queries num servidor MySQL e importar os dados para o R.


## Outras soluções para bases de dados grandes e sparklyr

Há outras soluções para lidarmos com bases de dados grandes. Em geral, elas demandam soluções que incluem o uso de outras ferramentas (Hadoop e Spark, por exemplo), ou o uso de outras linguagens em combinação com o R (C++, por exemplo). Há pacotes de R que implementam tais soluções -- _RHadoop_, _sparklyr_ e _Rcpp_, para as soluções menciondas, respectivamente. A complexidade de tais soluções é bastante mais elevada do que as apresentadas nesta primeira parte do curso e demanda conhecimento avançado em R.

Se o seu problema é apenas volume de dados e manipulação de bases simples, provavelmente as soluções apresentadas neste tutorial são suficientes. 

