# PA1_template
Ahmed Alashrafy  
Thursday, October 16, 2014  

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

## Loading and preprocessing the data

```r
library("plyr")


my_data<-read.csv("activity.csv",sep=",")
```

## What is the mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
aggregation.date<-ddply(my_data, c("date"),summarise,steps = sum(steps,na.rm=T))
barplot( aggregation.date$steps,names.arg = aggregation.date$date, xlab = "date", ylab = "steps")
```

![plot of chunk unnamed-chunk-2](.unnamed-chunk-2.png) 

2. Calculate and report the **mean** and **median** total number of
   steps taken per day


```r
mean(aggregation.date$steps,na.rm = T)
```

```
## [1] 9354
```

```r
median(aggregation.date$steps,na.rm=T)
```

```
## [1] 10395
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute
   interval (x-axis) and the average number of steps taken, averaged
   across all days (y-axis)


```r
aggregation.interval<-ddply(my_data, c("interval"),summarise,steps = mean(steps,na.rm=T))
plot(aggregation.interval, type = "l")
```

![plot of chunk unnamed-chunk-4](.unnamed-chunk-4.png) 

2. Which 5-minute interval, on average across all the days in the
   dataset, contains the maximum number of steps?


```r
aggregation.interval$interval[which.max(aggregation.interval$steps)]
```

```
## [1] 835
```


## Imputing missing values

1. Calculate and report the total number of missing values in the
   dataset (i.e. the total number of rows with `NA`s)


```r
sum(is.na(my_data))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the
   dataset. The strategy does not need to be sophisticated. For
   example, you could use the mean/median for that day, or the mean
   for that 5-minute interval, etc.



3. Create a new dataset that is equal to the original dataset but with
   the missing data filled in.Use the sum of the sterps per day then merg the two datasets according to date 


```r
new_my_data <- merge(my_data, aggregation.date, by = "date", suffixes = c(".all",".aggregated"))
```

4. Replace the NAs values


```r
new_my_data$steps.all[is.na(new_my_data$steps.all)] <- new_my_data$steps.aggregated[is.na(new_my_data$steps.all)]
new_my_data <- new_my_data[, c(1:3)]
```

5. Rename the column

```r
names(new_my_data)[2]<-"steps"
```



6.. Make a histogram of the total number of steps taken each day and
   Calculate and report the **mean** and **median** total number of
   steps taken per day. Do these values differ from the estimates from
   the first part of the assignment? What is the impact of imputing
   missing data on the estimates of the total daily number of steps?


```r
aggregation2.date <-ddply(new_my_data, c("date"),summarise,steps = sum(steps))
barplot( aggregation2.date$steps,names.arg = aggregation2.date$date, xlab = "date", ylab = "steps")
```

![plot of chunk unnamed-chunk-10](.unnamed-chunk-10.png) 

```r
mean(aggregation2.date$steps,na.rm=T)
```

```
## [1] 9354
```

```r
median(aggregation2.date$steps,na.rm=T)
```

```
## [1] 10395
```

The impact is too low.


## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels --
   "weekday" and "weekend" indicating whether a given date is a
   weekday or weekend day.


```r
weekdaytype <- function(date) {
    if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
        "weekend"
    } else {
        "weekday"
    }
}
new_my_data$weekparts <- as.factor(sapply(new_my_data$date, weekdaytype))
```

2. Make a panel plot containing a time series plot (i.e. `type = "l"`)
   of the 5-minute interval (x-axis) and the average number of steps
   taken, averaged across all weekday days or weekend days
   (y-axis).


```r
aggregation.weekdays<-ddply(new_my_data, c("weekparts","interval"),summarise,steps = mean(steps,na.rm=T))

par(mfrow=c(2,1))
plot(subset(aggregation.weekdays,weekparts=="weekend")[,c(2,3)], type="l", main="weekend")
plot(subset(aggregation.weekdays,weekparts=="weekday")[,c(2,3)], type="l", main="weekday")
```

![plot of chunk unnamed-chunk-12](.unnamed-chunk-12.png) 
