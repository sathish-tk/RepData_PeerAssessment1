---
title: "PA1_template"
output: html_document
---

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r}
library(ggplot2)

# read the data - assume the script is run on the same directory as the csv file
data <- read.csv("activity.csv")

data$date <- as.POSIXct(data$date)

# calculate the steps in a day using tapply historgram

steps_in_day <- tapply(data$steps, data$date, sum, na.rm=TRUE)
hist(steps_in_day)

#calculate the mean

dataMean <- mean(steps_in_day)
dataMean
# The mean is 9354

dataMedian <- median(steps_in_day)
dataMedian
# The median is 10395


#calculate interval steps
steps_interval <- aggregate(data$steps, by = list(data$interval), mean, na.rm=TRUE)
intstepsmed <- aggregate(data$steps, by = list(data$interval), median, na.rm=TRUE)

steps_interval <- cbind(steps_interval[], intstepsmed$x)

#Tidy the df names and round the numbers
names(steps_interval) = c("interval","mean.steps", "median.steps")
steps_interval$mean.steps <- round(steps_interval$mean.steps)
steps_interval$median.steps <- round(steps_interval$median.steps)


ggplot(steps_interval, aes(x = interval, y = mean.steps)) + geom_line()

mostSteps.steps <- steps_interval$interval[steps_interval$mean.steps == max(steps_interval$mean.steps)]

# The step interval each day is (835)-- "08:35"


# Input miising values

# total number of missing values

sum(is.na(data$steps))

# 2304 is the total missing values


#create gooddata set
# by remvoving the na values

goodData <- data

goodData$steps[is.na(goodData$steps)] <- 
  tapply(goodData$steps, goodData$interval, mean, na.rm = TRUE)

goodNumSteps <- tapply(goodData$steps, goodData$date, sum)

#generate goodData without missing values
qplot(
  goodNumSteps, 
  xlab='Total Steps Each Day', 
  ylab='Frequency')


# difference in activity patterns between weekdays and weekends


# weekday and weekend identifier

goodData$week <- ifelse(weekdays(goodData$date) == "Saturday" | weekdays(goodData$date) == "Sunday" ,"weekend","weekday")

# difference between the mean and median number of steps
steps2 <- aggregate(goodData$steps, by = list(goodData$week, goodData$interval), mean, na.rm=TRUE)
intstepsmed2 <- aggregate(goodData$steps, by = list(goodData$week, goodData$interval), median, na.rm=TRUE)

steps2 <- cbind(steps2[], intstepsmed2$x)

names(steps2) = c("weekday", "interval","mean.steps", "median.steps")
steps2$mean.steps <- round(steps2$mean.steps)
steps2$median.steps <- round(steps2$median.steps)


ggplot(steps2, aes(x = interval, y = mean.steps)) + ylab("Number of Steps") + geom_line() + facet_grid(weekday~.)

# the graph shows the users walk late on the weekends as compared to weekdays but more steps
```

