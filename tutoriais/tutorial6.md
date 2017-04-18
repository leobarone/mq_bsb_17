# Tutorial 6 - Exportando dados

Vamos começar a tarefa simples de exportar uma tabela. Vamos usar como exemplo o _data frame_ "valores_munic" que criamos ao final do tutorial anterior.

Para exportar uma tabela para um arquivo de texto, podemos usar a função _write.table_, ou suas variações, _write.csv_ e _write.csv2_. A diferença entre os três é o separador de colunas utilizado. _write.table_ utilizar "tab",  _write.csv_, vírgula e _write.csv2_, ponto e vírgula. Veja como é simples:

```{r}
write.csv2(valores_munic, 'valores_munic.csv')
```

Alternativas mais rápidas e similares provém do pacote _readr_ (já importado quando usamos _dplyr_). _write\_csv_ e _write\_excel\_csv_

```{r}
write_csv(valores_munic, 'valores_munic.csv')
```

ou

```{r}
write_excel_csv(valores_munic, 'valores_munic.csv')
```

Para escolher qual é o separado de colunas, use _write\_delim_ e defina o parâmetro _delim_ (no exemplo a seguir, ponto e vírgula)

```{r}
write_delim(valores_munic, 'valores_munic.csv', delim = ";")
```
