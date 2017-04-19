external file data frame

```{r}
arquivo_csv <- "~/mq_bsb_17/dados/fake_data.csv"
arquivo_xdf <- "~/mq_bsb_17/dados/fake_data.xdf"
```

```{r}
rxImport(inData = arquivo_csv, outFile = arquivo_xdf, overwrite = TRUE)
```


```{r}
rxGetInfo(data = arquivo_xdf, 
          getVarInfo = T,
          numRows = 10)
```

```{r}
rxSummary(formula = ~ sex + income, 
          data = arquivo_xdf)
```

```{r}
myLMobj <- rxLinMod(formula = income ~ savings, 
         data = myAirlineXdf)
```

