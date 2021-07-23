---
title: "1_Nao_supervionado_USP"
author: "IVaney Vieira de Sales"
date: "22/06/2021"
output: html_document
runtime: shiny
---

# Aula da professora Adriana

## CHAMANDO BIBLIOTECAS IMPORTANTES


```r
library(tidyverse) #pacote para manipulacao de dados
library(cluster) #algoritmo de cluster
library(dendextend) #compara dendogramas
library(factoextra) #algoritmo de cluster e visualizacao
library(fpc) #algoritmo de cluster e visualizacao
library(gridExtra) #para a funcao grid arrange
library(readxl)
```

## CLUSTER HIERARQUICO - juntos


```r
alunos_pap <- read.table("dados/alunos_pap.csv", sep = ";", header = T, dec = ",")
alunos_pap
```

```
##   Aluno Matematica Portugues
## 1   Dri          9         7
## 2    Li          5         4
## 3   Bru          6         6
## 4    Mi         10         8
## 5    Re          4         4
## 6    Ze          4         9
```

```r
rownames(alunos_pap) <- alunos_pap[,1]
alunos_pap <- alunos_pap[,-1]
```

### CALCULANDO MATRIZ DE DISTANCIAS


```r
d <- dist(alunos_pap, method = "euclidean")
d
```

```
##          Dri       Li      Bru       Mi       Re
## Li  5.000000                                    
## Bru 3.162278 2.236068                           
## Mi  1.414214 6.403124 4.472136                  
## Re  5.830952 1.000000 2.828427 7.211103         
## Ze  5.385165 5.099020 3.605551 6.082763 5.000000
```

### DEFININDO O CLUSTER A PARTIR DO METODO ESCOLHIDO
#### metodos disponiveis "average", "single", "complete" e "ward.D"

```r
hc1 <- hclust(d, method = "single" )
hc2 <- hclust(d, method = "complete" )
hc3 <- hclust(d, method = "average" )
hc4 <- hclust(d, method = "ward.D" )
```

### DESENHANDO O DENDOGRAMA

```r
plot(hc1, cex = 0.6, hang = -1)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

```r
plot(hc2, cex = 0.6, hang = -1)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-2.png)

```r
plot(hc3, cex = 0.6, hang = -1)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-3.png)

```r
plot(hc4, cex = 0.6, hang = -1)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-4.png)

### BRINCANDO COM O DENDOGRAMA PARA 2 GRUPOS


### COMPARANDO DENDOGRAMAS
#### comparando o metodo average com ward

```r
dend3 <- as.dendrogram(hc3)
dend4 <- as.dendrogram(hc4)
dend_list <- dendlist(dend3, dend4) 
```

#### EMARANHADO, quanto menor, mais iguais os dendogramas sao

```r
tanglegram(dend3, dend4, main = paste("Emaranhado =", round(entanglement(dend_list),2)))
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)
##### agora comparando o metodo single com complete

```r
dend1 <- as.dendrogram(hc1)
dend2 <- as.dendrogram(hc2)
dend_list2 <- dendlist(dend1, dend2) 
```
#### EMARANHADO, quanto menor, mais iguais os dendogramas sao

```r
tanglegram(dend1, dend2, main = paste("Emaranhado =", round(entanglement(dend_list2),2)))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

#### criando 2 grupos de alunos

```r
grupo_alunos2 <- cutree(hc4, k = 2)
table(grupo_alunos2)
```

```
## grupo_alunos2
## 1 2 
## 2 4
```

#### transformando em data frame a saida do cluster

```r
alunos_grupos <- data.frame(grupo_alunos2)
```

#### juntando com a base original

```r
Base_alunos_fim <- cbind(alunos_pap, alunos_grupos)
```

### entendendo os clusters
#### FAZENDO ANALISE DESCRITIVA
##### MEDIAS das variaveis por grupo

```r
mediagrupo_alunos <- Base_alunos_fim %>% 
  group_by(grupo_alunos2) %>% 
  summarise(n = n(),
            Portugues = mean(Portugues), 
            Matematica = mean(Matematica))
mediagrupo_alunos
```

```
## # A tibble: 2 x 4
##   grupo_alunos2     n Portugues Matematica
##           <int> <int>     <dbl>      <dbl>
## 1             1     2      7.5        9.5 
## 2             2     4      5.75       4.75
```

# CLUSTER HIERARQUICO - MCDonald

## Carregar base de dados: 

```r
mcdonalds <- read.table("dados/MCDONALDS.csv", sep = ";", dec = ",", header = T)
```
### transformar o nome dos lanches em linhas

```r
rownames(mcdonalds) <- mcdonalds[,1]
mcdonalds <- mcdonalds[,-1]
```

### Padronizar variaveis

```r
mcdonalds.padronizado <- scale(mcdonalds)
```

### calcular as distancias da matriz utilizando a distancia euclidiana

```r
distancia <- dist(mcdonalds.padronizado, method = "euclidean")
```

### Calcular o Cluster: metodos disponiveis "average", "single", "complete" e "ward.D"

```r
cluster.hierarquico <- hclust(distancia, method = "single" )
```
## Dendrograma

```r
plot(cluster.hierarquico, cex = 0.6, hang = -1)
```

![plot of chunk unnamed-chunk-20](figure/unnamed-chunk-20-1.png)
### Criar o grafico e destacar os grupos


### VERIFICANDO ELBOW 

```r
fviz_nbclust(mcdonalds.padronizado, FUN = hcut, method = "wss")
```

![plot of chunk unnamed-chunk-22](figure/unnamed-chunk-22-1.png)

### criando 4 grupos de lanches

```r
grupo_lanches4 <- cutree(cluster.hierarquico, k = 4)
table(grupo_lanches4)
```

```
## grupo_lanches4
##  1  2  3  4 
## 21  1  1  2
```
### transformando em data frame a saida do cluster

```r
Lanches_grupos <- data.frame(grupo_lanches4)
```
### juntando com a base original

```r
Base_lanches_fim <- cbind(mcdonalds, Lanches_grupos)
```
## FAZENDO ANALISE DESCRITIVA
### MEDIAS das variaveis por grupo

```r
mediagrupo <- Base_lanches_fim %>% 
  group_by(grupo_lanches4) %>% 
  summarise(n = n(),
            Valor.Energetico = mean(Valor.Energetico), 
            Carboidratos = mean(Carboidratos), 
            Proteinas = mean(Proteinas),
            Gorduras.Totais = mean(Gorduras.Totais), 
            Gorduras.Saturadas = mean(Gorduras.Saturadas), 
            Gorduras.Trans = mean(Gorduras.Trans),
            Colesterol = mean(Colesterol), 
            Fibra.Alimentar = mean(Fibra.Alimentar), 
            Sodio = mean(Sodio),
            Calcio = mean(Calcio), 
            Ferro = mean(Ferro) )
mediagrupo
```

```
## # A tibble: 4 x 13
##   grupo_lanches4     n Valor.Energetico Carboidratos Proteinas Gorduras.Totais
##            <int> <int>            <dbl>        <dbl>     <dbl>           <dbl>
## 1              1    21             445.         40.2      24              20.9
## 2              2     1             843          45        41              55  
## 3              3     1             425          31        39              16  
## 4              4     2             862          56.5      52.5            47.5
## # … with 7 more variables: Gorduras.Saturadas <dbl>, Gorduras.Trans <dbl>,
## #   Colesterol <dbl>, Fibra.Alimentar <dbl>, Sodio <dbl>, Calcio <dbl>,
## #   Ferro <dbl>
```

# CLUSTER NAO HIERARQUICO - Mcdonald

## AGRUPANDO LANCHES PELO METODO NAO HIERARQUICO

### Rodar o modelo

```r
mcdonalds.k2 <- kmeans(mcdonalds.padronizado, centers = 2)
```

### Visualizar os clusters

```r
fviz_cluster(mcdonalds.k2, data = mcdonalds.padronizado, main = "Cluster K2")
```

![plot of chunk unnamed-chunk-28](figure/unnamed-chunk-28-1.png)

### Criar clusters

```r
mcdonalds.k3 <- kmeans(mcdonalds.padronizado, centers = 3)
mcdonalds.k4 <- kmeans(mcdonalds.padronizado, centers = 4)
mcdonalds.k5 <- kmeans(mcdonalds.padronizado, centers = 5)
```

### Criar graficos

```r
G1 <- fviz_cluster(mcdonalds.k2, geom = "point", data = mcdonalds.padronizado) + ggtitle("k = 2")
G2 <- fviz_cluster(mcdonalds.k3, geom = "point",  data = mcdonalds.padronizado) + ggtitle("k = 3")
G3 <- fviz_cluster(mcdonalds.k4, geom = "point",  data = mcdonalds.padronizado) + ggtitle("k = 4")
G4 <- fviz_cluster(mcdonalds.k5, geom = "point",  data = mcdonalds.padronizado) + ggtitle("k = 5")
```

### Imprimir graficos na mesma tela

```r
grid.arrange(G1, G2, G3, G4, nrow = 2)
```

![plot of chunk unnamed-chunk-31](figure/unnamed-chunk-31-1.png)

### VERIFICANDO ELBOW 

```r
fviz_nbclust(mcdonalds.padronizado, kmeans, method = "wss")
```

![plot of chunk unnamed-chunk-32](figure/unnamed-chunk-32-1.png)

# CLUSTER NAO HIERARQUICO - Municipios

###  carregar base municipio

```r
municipios <- read.table("dados/municipios.csv", sep = ";", header = T, dec = ",", encoding = "UTF-8")
rownames(municipios) <- municipios[,1]
municipios <- municipios[,-1]
```

### padronizar dados

```r
municipios.padronizado <- scale(municipios)
```


### Agora vamos rodar de 3 a 6 centros  e visualizar qual a melhor divisao

```r
municipios.k3 <- kmeans(municipios.padronizado, centers = 3)
municipios.k4 <- kmeans(municipios.padronizado, centers = 4)
municipios.k5 <- kmeans(municipios.padronizado, centers = 5)
municipios.k6 <- kmeans(municipios.padronizado, centers = 6)
```

### Graficos

```r
G1 <- fviz_cluster(municipios.k3, geom = "point", data = municipios.padronizado) + ggtitle("k = 3")
G2 <- fviz_cluster(municipios.k4, geom = "point",  data = municipios.padronizado) + ggtitle("k = 4")
G3 <- fviz_cluster(municipios.k5, geom = "point",  data = municipios.padronizado) + ggtitle("k = 5")
G4 <- fviz_cluster(municipios.k6, geom = "point",  data = municipios.padronizado) + ggtitle("k = 6")
```

### Criar uma matriz com 4 graficos

```r
grid.arrange(G1, G2, G3, G4, nrow = 2)
```

![plot of chunk unnamed-chunk-37](figure/unnamed-chunk-37-1.png)

## VERIFICANDO ELBOW 

```r
fviz_nbclust(municipios.padronizado, FUN = hcut, method = "wss")
```

![plot of chunk unnamed-chunk-38](figure/unnamed-chunk-38-1.png)

### juntando dados

```r
municipios2 <- read.table("dados/municipios.csv", sep = ";", header = T, dec = ",")
municipiosfit <- data.frame(municipios.k6$cluster)
```

### Agrupar cluster e base

```r
MunicipioFinal <-  cbind(municipios2, municipiosfit)
head(MunicipioFinal)
```

```
##                                   Munic.pio Pop_Menos_15Anos_2014
## S?o Paulo                         S?o Paulo                 19.73
## Campinas                           Campinas                 18.17
## Guarulhos                         Guarulhos                 22.50
## Ribeir?o Preto               Ribeir?o Preto                 18.24
## Jundia?                             Jundia?                 18.24
## S?o Bernardo do Campo S?o Bernardo do Campo                 19.39
##                       Pop_com_60Anoso_2014 Habitantes    area taxa_natalidade
## S?o Paulo                            13.18   11245983 1521.10           15.09
## Campinas                             13.75    1079140  794.43           13.93
## Guarulhos                             9.57    1220653  318.68           16.92
## Ribeir?o Preto                       13.80     603774  650.96           13.15
## Jundia?                              14.55     369710  431.17           14.26
## S?o Bernardo do Campo                11.93     764922  409.48           14.13
##                       Esgoto_2010 Empregos_formais_2013       PIB
## S?o Paulo                   92.26               5247898 499375.40
## Campinas                    87.02                436561  42766.02
## Guarulhos                   86.90                354409  44670.72
## Ribeir?o Preto              97.48                230619  20300.80
## Jundia?                     95.90                181170  23712.62
## S?o Bernardo do Campo       90.26                292028  34185.28
##                       municipios.k6.cluster
## S?o Paulo                                 2
## Campinas                                  6
## Guarulhos                                 5
## Ribeir?o Preto                            1
## Jundia?                                   1
## S?o Bernardo do Campo                     5
```


