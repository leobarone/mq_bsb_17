# Tutorial B2 - R, conexão ODBC e Teradata

Neste brevíssimo tutorial vamos explorar uma possibilidade de conexão entre R e Teradata via ODBC. É possível enviar queries diretamente ao Teradata em linguagem SQL. Não há grandes vantagens nesse método a não ser a facilidade de importação de dados do Teradata para o R com a finalidade de produzir análises. A melhor solução para a integração Teradata-R é utiliar o Microsoft R Cliente, que é apresentado no Tutorial E1.

O conteúdo deste tutorial é genérico suficiente para ser aplicado a qualquer SGBD que permita conexão via ODBC, e não apenas à integração R e Teradata.

O primeiro requisito é ter um driver para conexão OBDC com Teradata em seu computador (você provavelmente tem em seu computador de trabalho se faz uso do Teradata). O segundo é baixar e carregar o pacote _RDOBC_:

```{r}
install.packages(RODBC)
library(RODBC)
```

## Estabelecendo uma conxão ODBC

Se, por exemplo, o driver para conexão chamar-se "TERADATA", podemos utilizar a função _odbcConnect_ para estabelecer a conexão, como abaixo:

```{r}
conexao <- odbcConnect(dsn = "TERADATA", uid = "user", pwd = "password")
```

É possível que sua conexão ODBC com o Teradata dispense o uso dos parâmetros de usuário e senha na função _odbcConnect_.

Use _sqlTables_ para lisatr as tabelas no Teradata:

```{r}
sqlTables(conexao)
```

## Lendo, escrevendo e descartando uma tabela

Para trazer ao R uma tabela do TERADATA ao qual você está conectado, use a função _sqlFetch_ com dois argumentos: o objeto de conexão e o nome da tabela, tal como no exemplo:

```{r}
sqlFetch(conexao, minha_tabela_sql) 
```

Se seu objetivo for fazer o upload de uma tabela (por exemplo, um data frame chamado dados que está na sua sessão de R), use _sqlSave_:

```{r}
sqlSave(conexao, dados, tablename = "minha_nova_tabela")
```

Para adicionar ("append") novos dados (que estão no data frame "novos_dados", por exemplo) em uma tabela já existente, use _sqlSave com o argumento "append" igual a TRUE ou _sqlUpdate_:

```{r}
sqlSave(conexao, novos_dados, tablename = "minha_nova_tabela", append = T)
# OU
sqlUpdate(conexao, novos_dados, tablename = "minha_nova_tabela")
```

Para descartar uma tabela no Teradata, sqlDrop:

```{r}
sqlDrop(conexao, "minha_tabela_descartavel") 
```

## Enviando queries em SQL

O lado ruim de fazer uma conexão ODBC, e não usar as funções do pacote _dplyr_ para conexão com algum SGBD, é não podermos utilizar os verbos de manipulação de dados que vimos em tutoriais anteriores. Para fazer a manipulação de dados no Teradata, precisamos escrever a query em SQL e enviá-la ao Teradata com a função _sqlQuery_:

```{r}
sqlQuery(conexao, "SELECT * FROM schema.tabela SAMPLE 20")
```

onde "SELECT * FROM schema.tabela SAMPLE 20" é uma query genérica.

Para trazer ao R o resultado da query como data frame utilize a função _sqlGetResults_:

```{r}
resultado_query <- sqlGetResults(conexao)
```

## Encerrando a conexão

Para encerrar a conexão use a função _close_

```{r}
close(conexao)
```

## Um pouco mais sobre RODBC

Para ler um pouco mais sobre conexões ODBC e R recomendo a "vignette" [ODBC Connectivity](https://cran.r-project.org/web/packages/RODBC/vignettes/RODBC.pdf).