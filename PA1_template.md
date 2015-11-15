# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data

First, load the raw data and the libraries used for processing the data.


```r
library(sqldf)
```

```
## Loading required package: gsubfn
## Loading required package: proto
## Loading required package: RSQLite
## Loading required package: DBI
```

```r
activity <- read.csv("activity.csv")
```
## What is mean total number of steps taken per day?


```r
sum_steps <- sqldf("select sum(steps) as steps, date 
                   from activity 
                   group by date 
                   order by date")
```

```
## Loading required package: tcltk
```

#### 1. Make a histogram of the total number of steps taken each day


```r
hist(sum_steps$steps, main = "Histogram of Steps Taken per Day", xlab = "Steps", col = "lightblue")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

#### 2. Calculate and report the mean and median total number of steps taken per day


```r
mean_steps <- as.integer(sqldf("select avg(steps) from sum_steps"))
median_steps <- median(sum_steps$steps, na.rm = TRUE)
```

The mean total number of steps taken per day is 10766.  
The median total number of steps taken per day is 10765.

## What is the average daily activity pattern?

#### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
avg_daily_act <- sqldf("select avg(steps) as steps, interval 
                       from activity 
                       group by interval 
                       order by interval")
plot(avg_daily_act$interval, avg_daily_act$steps, type = "l", 
     main = "Time series plot of interval vs average steps taken", 
     xlab = "Interval", ylab = "Avg Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max_steps <- as.integer(sqldf("select interval from avg_daily_act 
                              where steps = (select max(steps) from avg_daily_act)"))
```

Interval 835 has the most number of steps averaged across all the days in the dataset.

## Imputing missing values

#### Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
missing_values <- sum(is.na(activity$steps))
```

There are 2304 missing values in the raw data.

## Are there differences in activity patterns between weekdays and weekends?
