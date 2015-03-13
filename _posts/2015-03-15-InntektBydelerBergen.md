---
layout: post
title: Bydelsfattigdom Bergen 2013
---


![Figure not showing][id]


```r
## load necessary libraries
library(maptools)
library(ggplot2)
library(sp) 

## Workirectory
setwd("~/Dropbox/DataLek/2013 inntekt og fattigdom bergen")

## Install rgdal and rgeos
#setRepositories(ind = c(1,6))
#install.packages('rgdal')
#install.packages('rgeos')
library(rgdal)
library(rgeos)
```


```r
## Read map data from shapefile
map_bergen <- readShapePoly("bydel8f_2.shp")

# Necessary for ggplot2 fortify to run
#install.packages("gpclib")
gpclibPermit()
```




```r
## Read population data 
#jobb
sampledata <- read.csv("~/Dropbox/DataLek/2013 inntekt og fattigdom bergen/inntekt etter skatt bydeler bergen.csv", sep=";", dec=",")

# Reshape map for use by ggplot
f_bergen <- fortify(map_bergen, region="BYDEL")
```


```r
# Merge dataframe
sampledata$id <- sampledata$Bydel
sampledata$id  <-  toupper(sampledata$id)
map_and_data <- merge(f_bergen, sampledata, by="id")
```


```r
## Make labelled map
cnames <- aggregate(cbind(long, lat) ~ id, data=f_bergen, 
                    FUN=function(x)mean(range(x)))

cnames.old <- cnames

cnames$id <- c("Arna", "Årstad", "Åsane", "Bergenhus", "Fana", "Fyllingsdalen", "Laksevåg", "Ytrebygda")

labelled <- ggplot(f_bergen, aes(long, lat)) + geom_polygon(aes(group=group), colour="black", fill=NA) +  
  geom_text(data=cnames, aes(long, lat, label=id), size=3) +
  theme_bw() +
  theme(plot.background = element_blank(), 
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(), 
        axis.text.x = element_blank(), 
        axis.text.y = element_blank(),
        axis.ticks = element_blank(), 
        axis.title.x = element_blank(), 
        axis.title.y = element_blank())
```


```r
# Make populationplots
#table(sampledata$EU.skala.50.prosent)

## EU50
eu.50 <-  ggplot(data = map_and_data, aes(x = long, y = lat, group = id)) +
  geom_polygon(aes(fill = EU.skala.50.prosent)) + 
  geom_path(color="grey80") +
  labs(x = "Longitude", y = "Latitude") +
  scale_fill_distiller(palette="Blues", name="Andel (%)",
                        labels = c("< 3", "3-4","6.3", "9.7", "12.4"),
                        breaks = c(3, 4, 6.3, 9.7, 12.4))+
  guides(fill = guide_legend(reverse = TRUE)) +
  labs(title = "Bydelvis fordeling av personer under 18 år i privathusholdninger \nmed årlig inntekt under 50% av medianinntekten i Bergen (EU-skala)",
       fill = "") +
  theme_bw() +
  theme(plot.background = element_blank(), 
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(), 
        axis.text.x = element_blank(), 
        axis.text.y = element_blank(),
        axis.ticks = element_blank(), 
        axis.title.x = element_blank(), 
        axis.title.y = element_blank())
      
## EU 60
#table(sampledata$EU.skala.60.prosent)

eu.60 <-  ggplot(data = map_and_data, aes(x = long, y = lat, group = id)) + 
  geom_polygon(aes(fill = EU.skala.60.prosent)) + 
  geom_path(color="grey80") +
  labs(x = "Longitude", y = "Latitude") +
  scale_fill_distiller(palette="Blues", name="Andel (%)",
                       labels = c("< 6", "6-7","8-9", "12.7", "15.5", "21"),
                       breaks = c(6, 7, 9, 12.7, 15.5, 21))+
  guides(fill = guide_legend(reverse = TRUE)) +
  labs(title = 'Bydelvis fordeling av personer under 18 år i privathusholdninger \nmed årlig inntekt under 60% av medianinntekten i Bergen (EU-skala) \n("At-risk-of-poverty")',
       fill = "") +
  theme_bw() +
  theme(plot.background = element_blank(), 
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(), 
        axis.text.x = element_blank(), 
        axis.text.y = element_blank(),
        axis.ticks = element_blank(), 
        axis.title.x = element_blank(), 
        axis.title.y = element_blank()) 

## OECD50
# table(sampledata$OECD.skala.50.prosent)

oecd.50 <-  ggplot(data = map_and_data, aes(x = long, y = lat, group = id)) +
  geom_polygon(aes(fill = OECD.skala.50.prosent)) + 
  geom_path(color="grey80") +
  labs(x = "Longitude", y = "Latitude") +
  scale_fill_distiller(palette="Blues", name="Andel (%)",
                       labels = c("< 4", "4-5","5-6", "8.6", "11.4", "15.5"),
                       breaks = c(4, 5, 6, 8.6, 11.4, 15.5))+
  guides(fill = guide_legend(reverse = TRUE)) +
  labs(title = 'Bydelvis fordeling av personer under 18 år i privathusholdninger \nmed årlig inntekt under 50% av medianinntekten i Bergen (OECD-skala)',
       fill = "") +
  theme_bw() +
  theme(plot.background = element_blank(), 
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(), 
        axis.text.x = element_blank(), 
        axis.text.y = element_blank(),
        axis.ticks = element_blank(), 
        axis.title.x = element_blank(), 
        axis.title.y = element_blank()) 


## OECD60
# table(sampledata$OECD.skala.60.prosent)

oecd.60 <-  ggplot(data = map_and_data, aes(x = long, y = lat, group = id)) +
  geom_polygon(aes(fill = OECD.skala.60.prosent)) + 
  geom_path(color="grey80") +
  labs(x = "Longitude", y = "Latitude") +
  scale_fill_distiller(palette="Blues", name="Andel (%)",
                       labels = c("< 7", "8-9","9-10", "12.7", "15.7", "17.8", "24.5"),
                       breaks = c(7, 9, 10, 12.7, 15.7, 17.8, 24.5))+
  guides(fill = guide_legend(reverse = TRUE)) +
  labs(title = 'Bydelvis fordeling av personer under 18 år i privathusholdninger \nmed årlig inntekt under 60% av medianinntekten i Bergen (OECD-skala) \n("At-risk-of-poverty")',
       fill = "") +
  theme_bw() +
  theme(plot.background = element_blank(), 
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(), 
        axis.text.x = element_blank(), 
        axis.text.y = element_blank(),
        axis.ticks = element_blank(), 
        axis.title.x = element_blank(), 
        axis.title.y = element_blank()) 
```


```r
## Helper function from: 
# http://www.cookbook-r.com/Graphs/Multiple_graphs_on_one_page_(ggplot2)/

multiplot <- function(..., plotlist=NULL, file, cols=1, layout=NULL) {
  require(grid)
  
  # Make a list from the ... arguments and plotlist
  plots <- c(list(...), plotlist)
  
  numPlots = length(plots)
  
  # If layout is NULL, then use 'cols' to determine layout
  if (is.null(layout)) {
    # Make the panel
    # ncol: Number of columns of plots
    # nrow: Number of rows needed, calculated from # of cols
    layout <- matrix(seq(1, cols * ceiling(numPlots/cols)),
                     ncol = cols, nrow = ceiling(numPlots/cols))
  }
  
  if (numPlots==1) {
    print(plots[[1]])
    
  } else {
    # Set up the page
    grid.newpage()
    pushViewport(viewport(layout = grid.layout(nrow(layout), ncol(layout))))
    
    # Make each plot, in the correct location
    for (i in 1:numPlots) {
      # Get the i,j matrix positions of the regions that contain this subplot
      matchidx <- as.data.frame(which(layout == i, arr.ind = TRUE))
      
      print(plots[[i]], vp = viewport(layout.pos.row = matchidx$row,
                                      layout.pos.col = matchidx$col))
    }
  }
}
```


```r
## Other plots -  family structure
library(reshape2)
library(scales)

sampledata.2 <- sampledata
sampledata.2 <- sampledata.2[,c(1, 4:6)]
colnames(sampledata.2) <- c("Bydel", "Par uten barn", "Par med barn 0-17", "Enslig mor/far med barn 0-17")
 
sampledata.m <- melt(sampledata.2, id.vars="Bydel")
colnames(sampledata.m) <- c("Bydel", "Familiestruktur", "Husholdningsinntekt")
#View(sampledata.m)

p1 <- ggplot(sampledata.m, aes(Bydel, Husholdningsinntekt, group=Familiestruktur, colour=Familiestruktur)) + geom_line(lty="dotted") + 
  geom_point(size=5) +
  scale_y_continuous(labels=comma, name="Median husholdningsinntekt") +
  scale_x_discrete(limit=c("Arstad", "Laksevag", "Arna", "Bergenhus","Fyllingsdalen","Asane","Fana","Ytrebygda"), name="Bydel") + 
  theme_bw()+#eliminates background, gridlines, and chart border
  theme(
    plot.background = element_blank()
    ,panel.grid.major = element_blank()
    ,panel.grid.minor = element_blank()
    ,panel.border = element_blank() 
  ) +
  
  #draws x and y axis line
  theme(axis.line = element_line(color = 'black')) +
  theme(legend.key = element_blank()) +
  theme(axis.title.x = element_text(),
        axis.text.x  = element_text(angle=45, vjust=0.5, size=10))
```


```r
## Multiple plots on single page
#income plots
#multiplot(eu.50, eu.60, oecd.50, oecd.60, labelled, p1, cols=3)
```

[id]: maps.png "Bydelsfattigdom i Bergen 2013"

