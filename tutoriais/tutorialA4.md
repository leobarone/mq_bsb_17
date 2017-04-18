# Tutorial A4 - Dados de Redes Sociais - Twitter

Finalmente, vamos encerrar nosso tópico de captura de dados na internet aprendendo a capturar dados de uma rede social. Idealmente, trabalharíamos com o Facebook, dada sua popularidade. Infelizmente, o Facebook não dá acesso às postagens dos usuários e as possibilidades de acessar os dados dessa rede são bastante limitadas.

Por esta razão, trabalharemos nesta atividade com o Twitter, cuja API "Application Programming Interface" pode ser acessada gratuitamente.

# Twitter e R

Uma das grandes facilidades ao trabalharmos com o twitter é a possibildiade de capturar os dados via API. Isso significa que, diferentemente de quando raspamos páginas de internet, não precisamos capturar o códido HTML de um página do twitter para obter dados sobre tweets, usuários, etc. Basta enviarmos um "request" especifico via API e recebemos de volta os dados desejados.

Antes de enviar requisições de dados, porém, precisamos informar ao Twitter quem somos e conectarnos à API. Nosso primeiro passo, portanto, é criar uma conta no twitter. Você pode usar sua conta pessoal se desejar.

Uma vez criada a conta no Twitter, precisamos "criar um APP" específico para podermos nos conectar. Vá para o endereço https://apps.twitter.com/ e selecione "Create New App".

Preencha os dados. Note que o campo "Website" precisa ser informado, mas você pode usar uma página pessoal (por exemplo, seu github). Para o bom funcionamento da conexão, o campo "Callback URL" deve ser preenchido com "http://127.0.0.1:1410".

Volte para a página inicial do seu Twitter Apps. Escolha seu novo App. Clique na aba "Keys and Access Tokens". As chaves "Consumer Key (API Key)" e "Consumer Secret (API Secret)" são as informações que precisaremos para estabelecer uma conexão com o Twitter via R.

# Pacote twitteR - preparando o ambiente

Algumas das funções que vamos utilizar nesta atividade não estão na biblioteca básica do R. Temos, dessa forma, que começar instalando uma biblioteca chamada "twitteR". Esta biblioteca depende de outras duas, chamadas "ROAuth" (para autenticar conexões na web) e "httr" (para fazer requisições http, "http requests"). Vamos, assim, instalar as 3 bibliotecas:

```{r}
install.packages("ROAuth")
install.packages("httr")
install.packages("twitteR")
```

Uma vez instaladas as bibliotecas, as funções não estão automaticamente disponíveis. Para torná-las disponíveis é preciso "chamar" as bibliotecas:

```{r}
library(ROAuth)
library(httr)
library(twitteR)
```

# Estabelecendo uma conexão Twitter - R

Para conectar-se ao Twitter via R, utilizaremos a função "setup_twitter_oauth". Esta função recebe como parâmetros as "chaves" que você encontrará na página do App que você criou, Consumer Key (API Key)" e "Consumer Secret (API Secret)". É conveniente armazenar ambas em objtos de texto, como abaixo (note: as chaves abaixo são as minhas. Você pode usá-las, mas é legal substituir pelas suas para evitarmos múltiplas conexões):

```{r}
consumer_key <- 
consumer_secret <- 
access_token <- 
access_secret <- 
```

Usamos agora a função "setup_twitter_oauth" e nos conectamos ao Twitter via R:

```{r}
setup_twitter_oauth(consumer_key,
                    consumer_secret,
                    access_token,
                    access_secret)
```


Escolher a opção (1). Você será redirecionado ao browser para concluir a autenticação.

# Procurando tweets com #hashtags e termos de busca

Um dos nossos interesses será procurar tweets utilizando hashtags ou termos chave. A função "searchTwitter" faz isso com facilidade. Por exemplo, um dos assuntos nos "trending topics" de hoje é "Bolsa Familia". Vamos buscar os 300 últimos tweets com o termo:

```{r}
tweets <- searchTwitter("Bolsa Familia", n = 10000)
head(tweets)  
```

O resultado da busca é uma lista com 300 informações sobre os 300 tweets. Vamos olhar o tweet na primeira posição:

```{r}
class(tweets)
tweets[[1]]
```

Aparentemente recebemos apenas o texto do tweet. Porém, se examinamos com cuidado, vemos que a lista retornada contém elementos de uma classe de objetos bastante específica e pertencente ao pacote "twitteR":

```{r}
class(tweets[[1]])
```

Examinando sua estrutura com a função "str", encontramos as demais informações que nos interessam, tal como texto, data e hora, se é retweet, contagem de retweets, o "nome de tela" do usuário, o id do usuário, etc.

```{r}
str(tweets[[1]])
```

É incoveniente, porém, trabalharmos com o formato de lista. Considerando que todos os elementos da lista são da mesma classe de objetos, podemos reorganizá-los como uma matriz de dados, ou data frame. A função "twListToDF" faz isso automaticamente:

```{r}
df.tweets <- twListToDF(tweets)
```

O novo data frame contém 16 variáveis, cada um com uma das informações que visualizamos com o comando "str"

```{r}
names(df.tweets)
```

Fica fácil observar os 1000 primeiros tweets do data frame:

```{r}
View(df.tweets)
```

Ou obter um vetor com a contagem de retweets:

```{r}
df.tweets$retweetCount
```

Voltando à função "searchTwitter", convém inspecionar os parâmetros que podemos informar na busca:

```{r}
?searchTwitter
```

Além do número máximo de tweets a serem capturados, podemos "filtrar" a busca por idioma (lang), data inicial (since), data final (until), localidade ou proximidade radial a uma lat/long(geocode), entre outros parâmetros.

Finalmente, uma função útil para a análise de tweets é "strip_retweets", que remove todos os retweets da lista de tweets:

```{r}
tweets_noret <- strip_retweets(tweets, strip_manual=TRUE, strip_mt=TRUE)
```

# Usuários e timeline de usuários

Além de obtermos os tweets de uma busca por hashtag ou termo-chave, podemos obter informações dos usuários com a função "getUser". Por exemplo, obtemos o usuário MichelTemer e examinamos com "str" o conteúdo do resultado.

```{r}
temer_user <- getUser('MichelTemer')
str(temer_user)
```

Explorando os dados deste usuário

```{r}
temer_user$description      #descricao
temer_user$statusesCount    #tweets
temer_user$followersCount   #seguidores
temer_user$friendsCount     #amigos seguidos
temer_user$favoritesCount   #curtidas
temer_user$location         #localidade
temer_user$id               #id do usuario
```

Para obter a timeline de um usuário fazemos:

```{r}
temer_timeline <- userTimeline('MichelTemer', n = 100)
```

A timeline também pode ser transformada em data frame:

```{r}
df.timeline <- twListToDF(temer_timeline)
View(df.timeline)
```

# Trends

Por fim, podemos obter os trending topics do Twitter. Em primeiro lugar, observamos quais são as localidades para os quais trending topics são classificados:

```{r}
avail_trends <- availableTrendLocations()
head(avail_trends)
avail_trends[avail_trends$country == "Brazil",]
```

Com o código "woeid", obtemos os trends:

```{r}
trends <- getTrends(23424768)
head(trends)
```
