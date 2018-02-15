---
title: 'Reproducible Research: Peer Assessment 1'
output:
  html_document:
    keep_md: yes
  pdf_document: default
---


## Loading and preprocessing the data
1. Load the data
2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
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
activityData <- read.csv('activity.csv')
activityData$date <- as.Date(activityData$date, '%Y-%m-%d')
```

## What is mean total number of steps taken per day?
1. Calculate the total number of steps taken per day

```r
rst1_1 <- activityData %>% group_by(activityData$date) %>%
    summarise(totalSteps=sum(steps, na.rm = TRUE))
barplot(rst1_1$totalSteps, names.arg = rst1_1$'activityData$date', xlab="Days", ylab="Total Steps", main="Total Steps Per Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

2. Make a histogram of the total number of steps taken each day

```r
rst1_2 <- activityData %>% group_by(activityData$date) %>%
    summarise(totalSteps=sum(steps))
par(mar=c(5,5,3,3))
hist(rst1_2$totalSteps, xlab='Total Steps', main='Total Number of Steps Taken Each Day')
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day

```r
rst3Mean <- activityData %>% group_by(activityData$date) %>%
    summarise(totalSteps=sum(steps, na.rm = TRUE))
mean(rst3Mean$totalSteps)
```

```
## [1] 9354.23
```

```r
median(rst3Mean$totalSteps)
```

```
## [1] 10395
```


## What is the average daily activity pattern?
1. Make a time series plot (i.e. type='1') of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
rst2_1 <- activityData %>% group_by(interval) %>%
    summarise(meanval=mean(steps, na.rm = TRUE))
plot(x=rst2_1$interval, y=rst2_1$meanval, type="l",
     main="Daily Activity Pattern", xlab="Interval", ylab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
rst2_2 <- activityData %>% group_by(activityData$interval) %>%
    summarise(meanSteps=mean(steps, na.rm = TRUE)) %>%
    filter(meanSteps==max(meanSteps))
rst2_2$`activityData$interval`
```

```
## [1] 835
```

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
nrow(activityData %>% filter(is.na(steps)))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Since we don't have data for both 10-01 and 10-08, we use 0 to replace these values

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
newActivityData <- activityData
newActivityData[is.na(newActivityData$steps),1] <- 0
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
rst4_3 <- newActivityData %>% group_by(newActivityData$date) %>%
    summarise(totalSteps=sum(steps))
par(mar=c(5,5,3,3))
barplot(rst4_3$totalSteps, names.arg = rst4_3$'activityData$date', xlab="Days", ylab="Total Steps", main="Total Steps Per Day")
```

```
## Warning: Unknown or uninitialised column: 'activityData$date'.
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
hist(rst4_3$totalSteps, xlab='Total Steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-9-2.png)<!-- -->
Mean and Median

```r
rst4_4 <- newActivityData %>% group_by(newActivityData$date) %>%
    summarise(totalSteps=sum(steps))
mean(rst4_4$totalSteps)
```

```
## [1] 9354.23
```

```r
median(rst4_4$totalSteps)
```

```
## [1] 10395
```

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
newActivityData$dayOfWeek <- as.factor(weekdays(newActivityData$date))

levels(newActivityData$dayOfWeek) <- list(
    weekday = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday"),
    weekend = c("Saturday", "Sunday"))
```
2. Make a panel plot containing a time series plot (i.e. type='l') of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
rst5Weekday <- newActivityData %>% 
    filter(dayOfWeek=='weekday') %>%
    group_by(interval) %>%
    summarise(totalSteps=sum(steps))
rst5Weekend <- newActivityData %>% 
    filter(dayOfWeek=='weekend') %>%
    group_by(interval) %>%
    summarise(totalSteps=sum(steps))
par(mar=c(5,5,3,3),mfrow=c(2,1))
plot(x=rst5Weekday$interval,y=rst5Weekday$totalSteps, type='l', xlab='Total Steps',
     ylab='Total Steps', main = 'Weekday Pattern')
plot(x=rst5Weekend$interval,y=rst5Weekend$totalSteps, type='l', xlab='Total Steps',
     ylab='Total Steps', main = 'Weekend Pattern')
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
