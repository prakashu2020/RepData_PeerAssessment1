---
title: "PA1_template"
author: "Prakash Ukhalkar"
date: "8/12/2020"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r}
#Loading Data
setwd("D:/Reproducible Research/repdata_data_activity")
data<-read.csv("activity.csv")
```

```{r}
#Preprocessing of data
data$date<-as.Date(data$date)
```
```{r}
#Histogram without NA values
sum_steps<-aggregate(data$steps,by=list(data$date),FUN=sum,na.rm=TRUE) 

hist(sum_steps$x, 
      breaks=seq(from=0, to=25000, by=2500),
      col="yellow", 
      xlab="Total number of steps", 
      ylim=c(0, 20), 
      main="Histogram of the total number of steps taken each day\n(NA removed)")
```

```{r}
#Mean and Median of Steps
mean(sum_steps$x)
```
```{r}
median(sum_steps$x)
```
```{r}
#Time Series Plot
avg_steps<-aggregate(data$steps,by=list(data$interval),FUN=mean,na.rm=TRUE)

colnames(avg_steps)<-c("interval","steps")

library(ggplot2)
ggplot(aes(x=interval,y=steps),data=avg_steps)+geom_line()
```
```{r}
#Maximum Average 5 minute interval
avg_steps[avg_steps$steps==max(avg_steps$steps),1]
```
```{r}
#Imputing NA
sum(is.na(data$steps))
```
```{r}
#Replace NA values with the mean of the steps
data$steps[is.na(data$steps)]<-mean(data$steps,na.rm=TRUE)
head(data)
```

```{r}
#Histogram with Repaced NA values

sum_steps<-aggregate(data$steps,by=list(data$date),FUN=sum,na.rm=TRUE) 

hist(sum_steps$x, 
      breaks=seq(from=0, to=25000, by=2500),
      col="yellow", 
      xlab="Total number of steps", 
      ylim=c(0, 30), 
      main="Total number of steps taken each day\n(NA replaced by mean)")
```

```{r}
#Mean and median number of steps taken each day after replacing NA values with mean

mean(sum_steps$x)
```

```{r}
median(sum_steps$x)
```

```{r}
#Differences in activity patterns between weekdays and weekends

# Convert date into weekdays

data$days=tolower(weekdays(data$date))

#Now categorised days into weekend and weekdays

data$day_type<-ifelse(data$days=="saturday"|data$days=="sunday","weekend","weekday")

#Take mean steps taken on weekend or weekday in the intervals

avg_steps<-aggregate(data$steps,by=list(data$interval,data$day_type),FUN=mean,na.rm=TRUE)

colnames(avg_steps)<-c("interval","day_type","steps")

# Create panel plot between average steps and interval seperated by day type

ggplot(aes(x=interval,y=steps),data=avg_steps)+geom_line()+facet_wrap(~avg_steps$day_type)
```

