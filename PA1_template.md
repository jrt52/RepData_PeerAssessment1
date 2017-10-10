Loading and preprocessing the data
----------------------------------

1.Load the data

    url="https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
    library(downloader)
    download(url, dest="activity.zip", mode="wb")
    unzip("activity.zip", exdir=".")
    activity <- read.csv("activity.csv")

2.Process/transform the data (if necessary) into a format suitable for
your analysis

    activity$date <-as.Date(activity$date, format="%Y-%m-%d")

What is mean total number of steps taken per day?
-------------------------------------------------

For this part of the assignment, you can ignore the missing values in
the dataset.

1.Make a histogram of the total number of steps taken each day

    StepsPerD <-aggregate(steps ~ date, data= activity, sum, na.rm = TRUE)
    par(mfrow=c(1, 1))

    hist(StepsPerD$steps, breaks=20,
         main="Total Number of Steps Taken per Day", xlab= "Steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)
2.Calculate and report the mean and median total number of steps taken
per day

    mean(StepsPerD$steps)

    ## [1] 10766.19

    median(StepsPerD$steps)

    ## [1] 10765

What is the average daily activity pattern?
-------------------------------------------

1.Make a time series plot (i.e. type = "l" ) of the 5-minute interval
(x-axis) and the average number of steps taken, averaged across all days
(y-axis)

    av_step <-aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
    plot(av_step$interval, av_step$steps, type = "l", lwd=2,
         main="Time Series, Average Number of Steps per Interval Across All Days", axes = FALSE,
         xlab = " 5-minute interval No.", ylab="Average No. of Steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)
2.Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    av_step$interval[which.max(av_step$steps)]

    ## [1] 835

The 835th 5-minute interval containes the maximum number of steps.

Imputing missing values
-----------------------

Note that there are a number of days/intervals where there are missing
values (coded as NA ). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NA s)

    sum(is.na(activity))

    ## [1] 2304

There are 2304 missing values in this dataset.

2.Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc.

In order to fill in missing values in this data set, the mean number of
steps for that five-minute interval will be used

3.Create a new dataset that is equal to the original dataset but with
the missing data filled in.

    impute <- activity
    for(i in av_step$interval){
        impute[impute$interval == i & is.na(impute$steps), ]$steps <-
            av_step$steps[av_step$interval==i]
    }
    head(impute)

    ##       steps       date interval
    ## 1 1.7169811 2012-10-01        0
    ## 2 0.3396226 2012-10-01        5
    ## 3 0.1320755 2012-10-01       10
    ## 4 0.1509434 2012-10-01       15
    ## 5 0.0754717 2012-10-01       20
    ## 6 2.0943396 2012-10-01       25

4.Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day. Do these values differ from the estimates from the first part of
the assignment? What is the impact of imputing missing data on the
estimates of the total daily number of steps?

    StepsPerDI <-aggregate(steps ~ date, data = impute, sum, na.rm = TRUE)
    hist(StepsPerDI$steps, breaks = 20, 
         main = "Total Number of Steps Taken per Day(imputed)",
         xlab="Steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-9-1.png)

    mean(StepsPerDI$steps)

    ## [1] 10766.19

    median(StepsPerDI$steps)

    ## [1] 10766.19

Imputing had no impact on the mean, however the median is slightly
influences. Imputing using the average of the five minute interval
results in more data representing the mean, leading to less variation.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

"For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part."

1.Create a new factor variable in the dataset with two levels --
"weekday" and "weekend" indicating whether a given date is a weekday or
weekend day.

    impute$day <-weekdays(impute$date)
    impute$week <- ""
    impute[impute$day == "Saturday" | impute$day == "Sunday", ]$week <- "weekend"
    impute[!impute$day == "Saturday" | impute$day == "Sunday", ]$week <- "weekday"
    impute$week <-factor(impute$week)

2.Make a panel plot containing a time series plot (i.e. type = "l" ) of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis). The plot
should look something like the following, which was created using
simulated data:

    av_stepI <- aggregate(steps ~ interval + week, data = impute, mean)
    library(lattice)
    xyplot(steps ~ interval | week, data = av_stepI, type = "l", lwd = 2, 
           layout=c(1, 2),
           xlab="5-minute Interval",
           ylab= "Average Number of Steps",
           main= "Average No. of Steps (across all weekday or weekend days)")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png)
