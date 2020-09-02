---
title: 'Creating maps with Leaflet Package'
author: admin
date: '2020-09-01'
slug: Coursera-Leaflet
categories:
  - R
  - Spatial Analysis
tags: [R,Spatial Analysis, Yucatan Peninsula]
subtitle: 'Part of Course: Developing Data Products (Coursera - Johns Hopkins)'
summary: 'This is a exercise for Coursera and Johns Hopkins Specialization in Data Science'
authors: [admin]
lastmod: '2020-09-01T16:00:13-05:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
In this example, we created with `Leaflet` package. Also, I used `dplyr` for cleaning the data and `widgetframe` for render the map correctly.

This map shows water allocations according to REPDA by CONAGUA in Yucatán Península, México, by September 2020.
*Coordinates are not validated*
### First step: Download and data cleaning


```r
library(leaflet)
library(widgetframe)
```

```
## Loading required package: htmlwidgets
```

```r
library(htmlwidgets)
library(htmltools)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
url <-"http://201.116.60.46/DatosAbiertos/Otorgamiento_de_Concesiones.zip"

temp <- tempfile()
temp2 <- tempfile()
download.file(url, temp, mode="wb")
unzip(zipfile = temp, exdir = temp2)
data <- read.csv(file = paste0(temp2,"\\CONCESIONES","\\ANEXOS_SUBTERRANEOS.csv",sep=""),encoding = "UTF-8")
data1 <- read.csv(file = paste0(temp2,"\\CONCESIONES","\\CONCESIONES.csv",sep=""),encoding = "UTF-8")
data$lat=data$GRADOS.LATITUD+((1/60)*data$MINUTOS.LATITUD)+((1/3600)*data$SEGUNDOS.LATITUD)
data$lng=((data$GRADOS.LONGITUD)+((1/60)*data$MINUTOS.LONGITUD)+((1/3600)*data$SEGUNDOS.LONGITUD))*-1
data_all <- merge(x = data,y= data1, by = "X.U.FEFF.TITULO", all.x = TRUE)

df <- filter(data_all, NOMBRE.DE.ESTADO %in% c("CAMPECHE","QUINTANA ROO","YUCATAN"),lat>=17.5,lat<=22,lng<=-87,lng>=-92) #filter by State and coordinates

df <- data.frame(lat=c(df$lat),lng=c(df$lng),uso=c(df$USO.QUE.AMPARA.EL.TITULO),vol=df$VOLUMEN.ANUAL.EN.m3)
unlink(c(temp, temp2))

pal <- colorFactor(c("cyan3","darkgreen","sienna","grey77","dodgerblue4",
                     "red4","tan4","darkslategrey","gold3"),df$uso) #Set colors
```

### Generate the map with Leaflet package
**Clusters count number of allocation in the region. Meanwhile, circles describe each concession by type and volume**


```r
my_map <- leaflet() %>%
    addTiles() %>% # It generate the main layer
    addMarkers(lng=df$lng, lat=df$lat, popup = paste(df$uso,df$vol,"m3/year"),clusterOptions = markerClusterOptions()) %>% # Define marks
    addCircles(lng=df$lng, lat=df$lat,weight = 1, radius = sqrt(df$vol) * 2, color=pal(df$uso)) %>%
    addLegend(pal = pal,values = df$uso)%>%
    frameWidget()

my_map %>%
    frameWidget()
```

```
## Warning in frameWidget(.): Re-framing an already framed widget with new params
```

<!--html_preserve--><div id="htmlwidget-d048a0f7a84190e86d5e" style="width:100%;height:480px;" class="widgetframe html-widget"></div>
<script type="application/json" data-for="htmlwidget-d048a0f7a84190e86d5e">{"x":{"url":"/site/post/2020-09-01-coursera-leaflet.en_files/figure-html//widgets/widget_unnamed-chunk-2.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

```r
my_map
```

<!--html_preserve--><div id="htmlwidget-1f186e75e0def6e65a15" style="width:100%;height:480px;" class="widgetframe html-widget"></div>
<script type="application/json" data-for="htmlwidget-1f186e75e0def6e65a15">{"x":{"url":"/site/post/2020-09-01-coursera-leaflet.en_files/figure-html//widgets/widget_unnamed-chunk-2.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
Now, we use `htmlwidgets` to save the html file


```r
currentWD <- getwd()
dir.create("static/leaflet",showWarnings = FALSE)
#setwd("static/leaflet")
htmlwidgets::saveWidget(my_map, file ="my_map.html",selfcontained=TRUE)
#setwd(currentWD)
```
oa
<iframe seamless src="/static/post/2020-09-01-coursera-leaflet.en_files/my_map.html" width="100%" height="500"></iframe>
ob
<iframe seamless src="/static/post/2020-09-01-coursera-leaflet.en_files/my_map/index.html" width="100%" height="500"></iframe>
oc
<iframe seamless src="/static/leaflet/my_map.html" width="100%" height="500"></iframe>
od
<iframe seamless src="/static/leaflet/my_map/index.html" width="100%" height="500"></iframe>

opcion 2

```r
widgetframe::frameWidget(my_map,width='90%')
```

```
## Warning in widgetframe::frameWidget(my_map, width = "90%"): Re-framing an
## already framed widget with new params
```

<!--html_preserve--><div id="htmlwidget-4c7af2b9452e8ce42610" style="width:90%;height:480px;" class="widgetframe html-widget"></div>
<script type="application/json" data-for="htmlwidget-4c7af2b9452e8ce42610">{"x":{"url":"/site/post/2020-09-01-coursera-leaflet.en_files/figure-html//widgets/widget_unnamed-chunk-4.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

opcion 3

```r
frameWidget(my_map,width='90%')
```

```
## Warning in frameWidget(my_map, width = "90%"): Re-framing an already framed
## widget with new params
```

<!--html_preserve--><div id="htmlwidget-be8e778387f370be4c97" style="width:90%;height:480px;" class="widgetframe html-widget"></div>
<script type="application/json" data-for="htmlwidget-be8e778387f370be4c97">{"x":{"url":"/site/post/2020-09-01-coursera-leaflet.en_files/figure-html//widgets/widget_unnamed-chunk-5.html","options":{"xdomain":"*","allowfullscreen":false,"lazyload":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

