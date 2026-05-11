# Works today
## Script transformando Ptax diária em média trimestral

Enquanto escrevia parte de um projeto de pesquisa sobre a dívida pública brasileira me ocorreu que precisaria comrpar dados de sua composição. No entanto o primeiro problema a enfrentar é que os dados da dívida externa estão dolarizados no SGS do BCB. para ter uma ideia da proporção dessa parte da dívida no bolo total irei convertê-la para reais em moeda corrente. Para isso usarei a média trimestral da Ptax referente aquele valor da dívida.

Contudo a série de Ptax do BCB é diária sendo necessário calcular a média trimestral e transformar a tabela para que ambas, taxa de conversão e valor estejam coincidentes.

## Primeiro passo: carregar pacotes e criar as varáveis

```{r}
library(readxl)
library(dplyr)
library(lubridate)
library(openxlsx)

#variáveis de interesse
ptax <- read_excel("Ptax_serie.xlsx")

colnames (ptax)<- c("data","valor")

ptax$data <- as.Date(ptax$data)

ptax <- ptax %>%
  mutate (
    ano = year(data),
    trimestre = quarter(data)
  )

```
## Segundo Passo,agrupar por ano e trimestre e calcular a média

```{r}
ptax_trimestral <- ptax %>%
  group_by(ano, trimestre)%>%
  summarise(media_trimestral = mean(valor, na.rm = TRUE))%>%
  ungroup()
```
## Cria uma data final
Como meus dados do SGS do BCB para a dívida externa estão agrupados no dia 30 do último mês de cada trimestre, meus dados de média de Ptax também devem estar.
```{r}
ptax_trimestral <- ptax_trimestral %>%
  mutate(
    mes_final = trimestre*3,
    data = as.Date(paste(ano, mes_final, "30", sep = "-"))
  )

```
## Agrupando tudo
Por fim, vamos limpar o indesejável e pegar a planilha como queremos.

```{r}
ptax_trimestral <- ptax_trimestral %>%
  select(data, media_trimestral)

write.xlsx(ptax_trimestral,
           file = "Ptax_trimestral.xlsx",
           overwrite = TRUE)

```

Futuramente devo incluir também o link para o scraping direto pelo SGS.

Abs! :) 

