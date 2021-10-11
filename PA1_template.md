## 1)Loading and preprocesing the data

## 1\_1 Load the Data

We take the URL of the data and put it on a local directory with a copy
on Github, load the data into R

    # URL address of the data
    URL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"

    # download the file 
    download.file(URL,destfile="./datastore/repdata_2Fdata_2Factivity.zip",method="auto")


    # Unzip the file
    unzip(zipfile="./datastore/repdata_2Fdata_2Factivity.zip",exdir="data")

## 1\_2 Process/Transform the data into a format suitable for analysis

Cleaning the NA data

    data <- read.csv("data/activity.csv", header=TRUE)
    clear_Data <- na.omit(data)

## 2) What is mean total number os steps taken per day?

## 2\_1 Calculating Total number of steps per day

    steps_x_day <- aggregate(clear_Data$steps, by= list(Steps.Date= clear_Data$date), FUN = "sum")

## 2\_2 Histogram of the total number os steps taken each day

plot the number of steps in the x axis, divided in 20 breaks, and the y
shows the frecuency of how many days that quantity appear.

    hist(steps_x_day$x,
         breaks=20,
         main="Histogram total number steps x day",
         xlab= "Steps x day",
         ylab= "n° of days with this steps"
         )

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-1-1.png)
\#\# Calculating and reporting the mean and median of the total number
of \#\#steps taken per day

    #Mean
    mean(steps_x_day$x, na.rm=TRUE)

    ## [1] 10766.19

    #Median
    median(steps_x_day$x, na.rm=TRUE)

    ## [1] 10765

## 3) What is the average daily activity pattern?

## 3-1 Make a time series plot of the 5 minute interval vs average number of steps taken

    avg_steps_x_interval <- aggregate(steps ~ interval, clear_Data, mean)

    plot(avg_steps_x_interval$interval, avg_steps_x_interval$steps, type="l",
         col=1,
         main="Average number of steps by Interval",
         xlab="time interval",
         ylab="Average number of steps")

![](PA1_template_files/figure-markdown_strict/Plot%20daily%20activity%20pattern-1.png)

## 3\_2 Which 5-minute interval, contains the maximum number of steps?

    max_interval <- which.max(avg_steps_x_interval$steps)

    print (paste("The interval with the highest avg step is",avg_steps_x_interval[max_interval,]$interval," and the interval's step number is",round(avg_steps_x_interval[max_interval,]$steps, digits=1)))

    ## [1] "The interval with the highest avg step is 835  and the interval's step number is 206.2"

## 4) Imputing missing values

## 4\_1 Calculate and report the total number os missing values

    missing_value_act <- data[!complete.cases(data),]
    nrow(missing_value_act)

    ## [1] 2304

## 4\_2 Devise a strategy for filling missing values in the dataset

# Im’ going to replace the missing values (NA) with the average 5 minute interval

    data$full_steps <- ifelse(is.na(data$steps), round(avg_steps_x_interval$steps[match(data$interval,avg_steps_x_interval$interval)
    ],0),data$steps)

    head(data)

    ##   steps       date interval full_steps
    ## 1    NA 2012-10-01        0          2
    ## 2    NA 2012-10-01        5          0
    ## 3    NA 2012-10-01       10          0
    ## 4    NA 2012-10-01       15          0
    ## 5    NA 2012-10-01       20          0
    ## 6    NA 2012-10-01       25          2

\#\# 4\_3 Create a new dataset with the NA values filled

    # new dataframe

    full_data <- data.frame(steps=data$full_steps, interval= data$interval, date= data$date)

    head(full_data)

    ##   steps interval       date
    ## 1     2        0 2012-10-01
    ## 2     0        5 2012-10-01
    ## 3     0       10 2012-10-01
    ## 4     0       15 2012-10-01
    ## 5     0       20 2012-10-01
    ## 6     2       25 2012-10-01

## 4\_4 Make a Histogram of the total number of steps taken each day

    full_steps_x_day <- aggregate(full_data$steps, by= list(Steps.Date= full_data$date), FUN = "sum")

plot the number of steps in the x axis, divided in 20 breaks, and the y
shows the frecuency of how many days that quantity appear.

    hist(full_steps_x_day$x,
         breaks=20,
         main="Histogram total number steps x day",
         xlab= "Steps x day",
         ylab= "n° of days with this steps"
         )

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

Calculating the mean and median

    #Mean
    mean(full_steps_x_day$x, na.rm=TRUE)

    ## [1] 10765.64

    #Median
    median(full_steps_x_day$x, na.rm=TRUE)

    ## [1] 10762

The mean was reduced a litle bit, but the median keep the same value.
\#\# Are there differences in activity patterns between weekdays and
weekends?

## 5\_1 Create a new factor variable in the dataset with two levels- “weekday” “weekend” indicating whether a given date is a weekday pr weekend day

    full_data$newDate <- as.Date(full_data$date, format = "%Y-%m-%d")

    full_data$weekday <- weekdays(full_data$newDate)

    full_data$day_type <- ifelse(full_data$weekday=='Saturday' | 
    full_data$weekday=='Sunday', 'weekend','weekday')

    head(full_data, n=12)

    ##    steps interval       date    newDate weekday day_type
    ## 1      2        0 2012-10-01 2012-10-01   lunes  weekday
    ## 2      0        5 2012-10-01 2012-10-01   lunes  weekday
    ## 3      0       10 2012-10-01 2012-10-01   lunes  weekday
    ## 4      0       15 2012-10-01 2012-10-01   lunes  weekday
    ## 5      0       20 2012-10-01 2012-10-01   lunes  weekday
    ## 6      2       25 2012-10-01 2012-10-01   lunes  weekday
    ## 7      1       30 2012-10-01 2012-10-01   lunes  weekday
    ## 8      1       35 2012-10-01 2012-10-01   lunes  weekday
    ## 9      0       40 2012-10-01 2012-10-01   lunes  weekday
    ## 10     1       45 2012-10-01 2012-10-01   lunes  weekday
    ## 11     0       50 2012-10-01 2012-10-01   lunes  weekday
    ## 12     0       55 2012-10-01 2012-10-01   lunes  weekday

## 5\_2 Make a panel plot containing a time series plot of the 5-minute interval and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

    library(ggplot2)

    avg_full_data <- aggregate (steps ~ interval + day_type, data = full_data, mean)

    ggplot(avg_full_data, aes(interval, steps))+
            geom_line()+
            facet_grid(day_type ~.)+
            xlab("5-minute interval")+
            ylab("average n° steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-7-1.png)
