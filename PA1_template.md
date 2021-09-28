# Reproducible Research - Course Project 1
### Author: Boyd C
### Date: September 27, 2021

## Loading and preprocessing the data

### Read data and store in a dataframe 'activity'


```r
activity <- read.csv("course5/activity.csv", header = TRUE)
```

The csv file downloaded from the project link is stored in the folder
course5 under the Working Directory.
Quick review of data in the dataframe shows that the dataset is 
good enough to continue the futher process

## What is mean total number of steps taken per day?

### Histogram of the total number of steps taken each day

First calculate total number of steps taken per day and store 
in an array called totalstepsperday. Use this array to produce
required Histogram


```r
totalstepsperday <- with(activity, tapply(steps, date, sum))
hist(totalstepsperday, breaks = 15, col = "green", xlab = "Steps", main = "Histogram of Total Steps per day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)

### Mean and median number of steps taken each day


```r
paste("Mean of Total daily steps: ", as.character(round(mean(totalstepsperday, na.rm = T), digits = 2)))
```

```
## [1] "Mean of Total daily steps:  10766.19"
```

```r
paste("Median of Total daily steps: ", as.character(median(totalstepsperday, na.rm = T)))
```

```
## [1] "Median of Total daily steps:  10765"
```

## What is the average daily activity pattern?

### Time series plot of the average number of steps taken

Calculate Average steps taken for each interval and store in a dataframe 'avestepsperinterval'. Add column names to this dataframe to make it readable. Average Steps taken has been rounded to 2 decimal places, just for readability,. Use this dataframe to generate time series plot.


```r
avestepsperinterval <- aggregate(activity$steps,list(activity$interval), FUN=mean, na.rm = T)
colnames(avestepsperinterval) <- c("Interval", "Ave.Steps")
avestepsperinterval$Ave.Steps <- round(avestepsperinterval$Ave.Steps, digits = 2)
plot(avestepsperinterval$Ave.Steps, type = "l", col = "blue", xlab = "Interval", ylab = "Average Steps", main = "Plot depicting Average Steps taken over Interval", lwd = 2)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

### The 5-minute interval that, on average, contains the maximum number of steps


```r
paste("Intervel which has maximum average steps: ", as.character(avestepsperinterval[avestepsperinterval[,2] == max(avestepsperinterval$Ave.Steps), 1]))
```

```
## [1] "Intervel which has maximum average steps:  835"
```

## Imputing missing values

### Total number of missing values


```r
paste("Number of missing values: ", as.character(sum(is.na(activity$steps))))
```

```
## [1] "Number of missing values:  2304"
```

### Code to describe and show a strategy for imputing missing data

Decided to fill the missing values using the Median of steps taken for that specific interval. Calculate median steps taken for each interval and store in dataframe medstepsperinterval. Add column names to this dataframe for readability. Create another dataframe called 'filled.activity' similar to the original one. Fill the missing values using the median interval steps from 'medstepsperinterval'



```r
medstepsperinterval <- aggregate(activity$steps,list(activity$interval), FUN=median, na.rm = T)
colnames(medstepsperinterval) <- c("Interval", "Median.Steps")
filled.activity <- activity
numrow <- nrow(filled.activity)
for (i in 1:numrow) {
  if (is.na(filled.activity$steps[i])) {
    filled.activity$steps[i] <- medstepsperinterval[medstepsperinterval[,1] == filled.activity$interval[i],2]
  }
}
```

### Histogram of the total number of steps taken each day after missing values are imputed

Calculate the total number of steps taken per day using the filled dataframe and store it in the array 'totalstepsperday.filled'. Use this array to generate the Histogram


```r
totalstepsperday.filled <- with(filled.activity, tapply(steps, date, sum))
hist(totalstepsperday.filled, breaks = 15, col = "yellow", xlab = "Steps", main = "Histogram of Total Steps per day after filling")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)

### Mean and median of the total number of steps taken per day after filling


```r
paste("Mean of Total daily steps after filling: ", as.character(round(mean(totalstepsperday.filled, na.rm = T), digits = 2)))
```

```
## [1] "Mean of Total daily steps after filling:  9503.87"
```

```r
paste("Median of Total daily steps after filling: ", as.character(median(totalstepsperday.filled, na.rm = T)))
```

```
## [1] "Median of Total daily steps after filling:  10395"
```

Comparison and Hitogram and Mean and Median Values show that there is slight difference in the results due to filling

## Are there differences in activity patterns between weekdays and weekends?

Add another column to the dataframe filled.activity called WDay, this column will indicate if the date is a weekday or weekend. Create another dataframe 'avestepsoverintervelandwday' which has the Average step over interval and WDay. Add column names to avestepsoverintervelandwday for readability

### Create a new factor variable in the dataset with two levels


```r
wdays <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
filled.activity$WDay <- factor(weekdays(as.Date(filled.activity$date)) %in% wdays, labels = c("weekday", "weekend"), levels = c(TRUE, FALSE))
avestepsoverintervelandwday <- aggregate(filled.activity$steps ~ filled.activity$interval + filled.activity$WDay, FUN=mean, na.rm = T)
colnames(avestepsoverintervelandwday) <- c("Interval", "WDay", "Ave.Steps")
```

Use the dataframe avestepsoverintervelandwday to generate plot

###  Panel plot containing a time series plot


```r
par(mfrow = c(2, 1))
par(mar = c(4, 4, 2, 1))
with(avestepsoverintervelandwday[avestepsoverintervelandwday$WDay == "weekday",], plot(Interval, Ave.Steps, col = "green", type = "l", lwd = 1.5, ylab = "Average Steps", main = "Average Steps per time interval: weekday vs weekend"))
legend("topright", c("weekday", "weekend"), text.col = c("green", "blue"))
with(avestepsoverintervelandwday[avestepsoverintervelandwday$WDay == "weekend",], plot(Interval, Ave.Steps, col = "blue", type = "l", lwd = 1.5, ylab = "Average Steps"))
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

The End