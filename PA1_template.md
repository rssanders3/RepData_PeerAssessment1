Reproducible Research: Peer Assessment 1
========================================

## Pre-run Instructions

```r
#Install Knitr
install.packages("knitr")

#Unzip the activity.zip file. This should produce a file called activity.csv.
unzip("activity.zip")
```

## Loading and preprocessing the data

1. Load the data (i.e. read.csv())

```r
activityData = read.csv("activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis

None

## What is mean total number of steps taken per day?
#### For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

```r
activityStepsDateData = aggregate(steps ~ date, data=activityData, FUN=sum)
barplot(activityStepsDateData$steps, names.arg=activityStepsDateData$date, xlab="Date", ylab="Steps")
```

![plot of chunk makeHistogram](figure/makeHistogram.png) 

2. Calculate and report the mean and median total number of steps taken per day

```r
mean(activityStepsDateData$steps)
```

```
## [1] 10766
```

```r
median(activityStepsDateData$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
activityStepsIntervalData <- aggregate(steps ~ interval, data=activityData, FUN=mean)
plot(activityStepsIntervalData, type="l", xlab="Interval", ylab="Steps")
```

![plot of chunk plotStepsTakenAcrossDays](figure/plotStepsTakenAcrossDays.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
activityStepsIntervalData$interval[which.max(activityStepsIntervalData$steps)]
```

```
## [1] 835
```

## Imputing missing values
#### Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
activityNA = is.na(activityData)
activityStepsNA = is.na(activityData$steps)
length(activityNA[activityNA==TRUE])
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Setting means for the 5-minute intervals to missing values.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activityData = merge(activityData, activityStepsIntervalData, by="interval", suffixes=c("","_mean"))
activityData$steps[activityStepsNA] = activityData$steps_mean[activityStepsNA]
activityData = activityData[,c(1:3)]
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
activityStepsDateData = aggregate(steps ~ date, data=activityData, FUN=sum)
barplot(activityStepsDateData$steps, names.arg=activityStepsDateData$date, xlab="Date", ylab="Steps")
```

![plot of chunk histOfTotalNumberOfSteps](figure/histOfTotalNumberOfSteps.png) 

```r
mean(activityStepsDateData$steps)
```

```
## [1] 9564
```

```r
median(activityStepsDateData$steps)
```

```
## [1] 11216
```
The values do differ slightly. However, the impact of imputing missing data appears to be low.

## Are there differences in activity patterns between weekdays and weekends?
#### For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
getDateType = function(date) {
  if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) 
    return("weekend")
  else
    return("weekday")    
}
activityData$dayType = as.factor(sapply(activityData$date, getDateType))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
par(mfrow=c(2,1))

#Creating Weekend Plot
activityWeekendData = aggregate(steps ~ interval, 
                                data=activityData, 
                                subset=activityData$dayType=="weekend", 
                                FUN=mean)
plot(activityWeekendData, type="l", main="Weekend", xlab="Interval", ylab="Steps")

#Creating Weekday Plot
activityWeekdayData = aggregate(steps ~ interval, 
                                data=activityData, 
                                subset=activityData$dayType=="weekday", 
                                FUN=mean)
plot(activityWeekdayData, type="l", main="Weekday", xlab="Interval", ylab="Steps")
```

![plot of chunk plotTimeSeries](figure/plotTimeSeries.png) 
