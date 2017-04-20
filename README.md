# Análise e Sistematização de Big Data

Bem vind@s à página do curso "Análise e Sistematização de Big Data". Nesta página você encontrará os tutoriais, dados, e demais materiais que utilizaremos no curso, organizados por dia.

## Informações Básicas

Professor: Leonardo Sangali Barone [leobarone@gmail.com](leobarone@gmail.com)

Assistentes: Marina Merlo e Nathan Giachetta Valério Alves

Duração: 15 horas

## Materiais

### Dia 1 - 17/04/2017 - Introdução ao R

- Deixamos para a aula seguinte o [Tutorial 0 - Dois exemplos para começar](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial0.md)
- Começamos com o [Tutorial 1 - Bases de dados e data frames](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial1.md)
- E demos início ao [Tutorial 2 - Uma gramática para bases de dados: dplyr](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial2.md), com o qual trabalharemos no início da aula seguinte.
- A leitura obrigatória para esta primeira aula é: Aquino (2014) [R para cientistas sociais](http://www.uesc.br/editora/livrosdigitais_20140513/r_cientistas.pdf), capítulos 2 e 3
- Recomendado também a leitura de: Grolemund, Garrett. 2014. Hands-On Programming with R: Write Your Own Functions and Simulations. “O’Reilly Media, Inc.” – Parte 1

### Dia 2 - 18/04/2017 - Manipulação de dados no R

- Vamos aquecer com o [Tutorial 0 - Dois exemplos para começar](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial0.md). Você não precisa executar o exemplo 1, pois pode demorar muito para baixar os dados. Não perca muito tempo com este tutorial.
- Seguiremos com o [Tutorial 2 - Uma gramática para bases de dados: dplyr](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial2.md), que alguns tiveram a oportunidade de começar na aula anterior.
- No [Tutorial 3 - Bases de dados relacionais com a gramática básica do pacote dplyr](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial3.md) trabalharemos com bases de dados relacionais no R.
- Convém darmos também um passo atrás e observar várias maneiras de abrirmos dados no R com o [Tutorial 4 - Abrindo dados no R](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial4.md)
- Se sobrar tempo e energia para você em sala (e só se sobrar), faça como atividade extra o [Tutorial 5 - Manipulação de dados com a gramática básica do R](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorial5.md) para aprender sobre a gramática básica do R.
- A leitura obrigatória para esta primeira aula é: Aquino (2014) [R para cientistas sociais](http://www.uesc.br/editora/livrosdigitais_20140513/r_cientistas.pdf), capítulo 5
- Recomendo fortemente a leitura (em inglês) do livro Wickham, Hadley, and Garrett Grolemund. 2016. [R for Data Science: Import, Tidy, Transform, Visualize, and Model Data](http://r4ds.had.co.nz/). “O’Reilly Media, Inc.”. Os capítulos 5, 10 e 11 tratam dos temas que trabalhamos neste segundo dia, mas sugiro a leitura de todo o livro após o curso.
- Dica: [CheatSheet dplyr - inglês](https://github.com/rstudio/cheatsheets/raw/master/source/pdfs/data-transformation-cheatsheet.pdf)
- Dica: [CheatSheet dplyr - português](https://www.rstudio.com/wp-content/uploads/2016/03/data-wrangling-cheatsheet-portuguese.pdf)

### Dias 3 e 4 - 19/04/2017 e 20/04/2017 - Tópicos de Big Data

Como conversamos em sala, vamos trabalhar diferentes "faces" do termo "Big Data" nos dois dias finais de curso. Você poderá escolher quais tópicos deseja aprender durante a semana do curso e seguir com os demais se desejar. Lembre-se: há mais material abaixo do que cabe nas horas do curso. Priorize.

Antes de avançar, porém, tenha certeza que você terminou os tutoriais básicos de R. Os fundamentais são os tutoriais 1 a 3.

OBS1: adicionei ao final do tutorial 3 uma breve explicação sobre exportação de arquivos no R. Se já tiver concluído este tutorial, volte a ele quando precisar.

- A leitura obrigatória para as duas últimas aulas é: Aquino (2014) [R para cientistas sociais](http://www.uesc.br/editora/livrosdigitais_20140513/r_cientistas.pdf), capítulo 7 e 8.

#### A - Capturando dados com R

Neste tópico você aprenderá a raspar dados de páginas de internet. Uma das vantagens dos tutoriais deste tópico é lidar com temas de programação em R que não pudemos abordar anteriormente, sobretudo loops e listas.

- Comece pelo [Tutorial A1 - Webscrapping do portal da transparência - tabelas](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialA1.md), que é um exemplo simples de raspagem de tabelas em html.
- A seguir, faça o [Tutorial A2 - Webscrapping de um portais de notícias - ALESP](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialA2.md), que aprensenta aspectos mais complexos da captura de páginas html e faz uma rápida introdução ao XML.
- E finalize com o [Tutorial A3 - Webscrapping de um portais de notícias - DataFolha](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialA3.md), que constrói uma captura completa de sites de notícias.
- Quem tiver interesse, pode aprender sobre captura de dados do twitter com R no [Tutorial A4 - Dados de Redes Sociais - Twitter](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialA4.md)
- A principal referência (leitura recomendada) para este tópico é: Nolan, D., &amp; Temple Lang, D. (2014). XML and Web Technologies for Data Sciences with R. New York, NY: Springer New York.

#### B - Dados volumosos com R - conectando R a outras bases de dados

O R trabalha regurlamente armazenando os dados na RAM. Quando o volume de dado superar a memória RAM temos de buscar outras soluções. A solução mais simples é utilizar um sistema de gerenciamento de banco de dados (SGBD) que permita superar os limites de armazenamento de dados. Uma das soluções mais simples é utilizar um SGBD para a qual há pacotes em R que facilitem a conexão e manipulação de dados.

- Para ver como trabalhar com dados em MySQL, PostgreSQL, MariaDB, SQLite e BigQuery, faça o [Tutorial B1 - R, dplyr e SQL](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialB1.md). A grande vantagem de integram algum desses SGBD com o R é o fato de que não precisamos conhecer nada da linguagem SQL para manipular os dados no servidor (local ou remoto). Basta utilizar os verbos do _dplyr_ que vimos nos tutoriais anteriores.

NOTA: Tutorial atualizado para lidar com os problemas que apareceram em sala de aula com um servidor PostgreSQL e com uma pequena adição ao final para gerar tabelas permanentemente no servidor.

- Por conta da demanda de trabalho dos participantes do curso, apresento um tutorial rápido para conexão e manipulação de dados no Teradata via R usando ODBC no [Tutorial B2](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialB2.md).

#### C - Dados volumosos com R - bibliotecas do R

NOTA: Como ampliamos o número de tópicos do curso e decidimos priorizar aqueles mais relevantes para vocês, acabamos por deixar esse tema de lado. Trabalhar com as bibliotecas básicas do R para superar os problemas de dados que não cabem na RAM se tornou a opção menos atrativa. Vou manter o tópico indicado aqui e subo o tutorial C1 na próxima semana para quem tiver interesse.

#### D - Uma rápida introdução ao aprendizado de máquina com R

Big Data é sobre o volume de dados e sobre captura massiva. Mas também é sobre a análise dos dados. O aprendizado de máquina, que nada mais é do que a aplicação de técnicas estatísticas com a finalidade de previsão, classificação e agrupamento dos dados, é frequentemente associado à ideia imprecisa de Big Data.

- No [Tutorial D1 - Introdução ao Aprendizado de Máquina](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialD1.md) veremos rapidamente e de forma bastante introdutória os alguns algoritmos de aprendizado de máquinas
- Recomendo fortemente a leitura futura do livro James, G; Witten, D; Hastie, T ;and Tibshirani, R., (2013). [An Introduction to Statistical Learning](http://www-bcf.usc.edu/~gareth/ISL/), disponibilizado gratuitamente pelos autores. Os capítulos 2, 3 e 10 tratam dos temas do tutorial acima.

#### E - Microsoft R Client e o pacote RevoScaleR

Uma das soluções atraentes de Big Data no R, porém com software proprietário, é utilizar o Microsoft R Client e as funções do pacote _RevoScaleR_. É uma solução atraente por 3 razões: 1- permite computação em outros "contextos", como Teradata, Haddop e servidores SQL; 2- oferece uma solução para computação local que não está limitada pelo tamanho da RAM; e 3- oferece bons algoritmos para aprendizado de máquina.

É preciso instalar o Microsoft R Client e você encontra informações sobre a instalação [aqui](https://msdn.microsoft.com/en-us/microsoft-r/r-client-get-started), disponível para Microsoft e Linux.

- Faça o [Tutorial E1](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialE1.md) para uma breve introdução à "gramática" do _RevoScaleR_ e suas funções para aprendizado de máquina. Dicas de onde continuar aprendendo e como integrar com Teradata no tutorial.
- Como não haverá instalação do Microsoft R Client no laboratório, você pode preferir fazer o [Tutorial E1 na versão pdf](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialE1.pdf), no qual os resultados dos códigos estão impressos.

#### F - R, Spark e sparklyr para Aprendizado de Máquina

Apache Spark é um framework de código aberto para computação e processamento de dados em paralelo e é uma das soluções mais atraentes para aprendizado de máquina com velocidade e para um grande volume de dados. Com o pacote _sparklyr_, da RStudio, conectar R e Spark é bastante simples, pois podemos utiliar os verbos do _dplyr_ e as funções básicas de aprendizado de máquina estão implementadas.

- No [Tutorial F1](https://github.com/leobarone/mq_bsb_17/blob/master/tutoriais/tutorialF1.md) veremos um pouco do pacote _sparklyr_.

- Ainda há pouca documentação sobre _sparklyr_ e a [homepage do projeto](http://spark.rstudio.com/) é o melhor lugar a buscar.

### Pós-curso

- [Shiny Apps](http://shiny.rstudio.com/) - Framework para construção de aplicativos e apresentação interativa de dados na web.
- [Shiny Dashboards](http://rstudio.github.io/shinydashboard/index.html) - Pacote de R para criar Dashboard em Shiny
- [R e Gis]() - Disponível aqui até o fim de junho
- [R e texto]() - Disponível aqui até o fim de junho
- [Datas com lubridate](http://r4ds.had.co.nz/dates-and-times.html) - capítulo 16 de R for Data Science
- Guia de instalação de R e Rstudio [aqui](http://www.leg.ufpr.br/~fernandomayer/ensino/ce083-2015-02/ce083-2015-02-instalacao-R.html)