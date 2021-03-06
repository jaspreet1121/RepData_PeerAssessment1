
To read the file using `read.csv`


```r
act<-read.csv("activity.csv")
```

To move forward we separate hours and minutes from interval variable of the dataset. We also combine these `hour` and `minute` variable with the `date` variable to form the new `datex` variable.


```r
act$hour<-as.integer(act$interval/100)
act$minute<-act$interval%%100
act$interval_new<-act$hour*60+act$minute
act$date_new<-as.POSIXlt(act$date)
```

To get the total number of steps per day we use the `aggregate` function and we assign that to the `spd` variable.
To get the mean of steps per interval we use the `aggregate` function and we assign that to the `mpd` variable.


```r
spd<-aggregate(steps~date,data = act,sum,na.rm=TRUE)
mpd<-aggregate(steps~interval_new,data = act,mean,na.rm=TRUE)
```

To plot the histogram of the number of steps taken on each day.


```r
hist(spd$steps,col = "orange",main = "Histogram of steps taken per day",xlab = "Number of steps taken each day",breaks = 10)
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

To take the mean and median of the no. of steps per day


```r
median_spd<-median(spd$steps)
mean_spd<-as.integer(mean(spd$steps))
median_spd
```

```
## [1] 10765
```

```r
mean_spd
```

```
## [1] 10766
```

To make the time series plot of the 5-minute interval and the average number of steps taken.


```r
plot(steps~interval_new,data = act,type = "l",main = "5-minute interval and the average number of steps taken",xlab = "Interval",ylab = "Steps averaged across the days")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

To find which 5-minute interval has the maximum value of steps


```r
which.max(mpd$steps)
```

```
## [1] 104
```

The total number of NA values in the data set is


```r
sum(is.na(act$steps))
```

```
## [1] 2304
```

The NA value for a particular instance would be assigned the mean value of the corresponding time interval.

For this we use a `for` loop, we check if the value is NA, whenever it is true we assigne the mean value of the time interval to that.


```r
act_new<-act
for(x in 1:nrow(act_new)){
     if(is.na(act_new$steps[x])){
         y<-which(mpd$interval_new==act_new$interval_new[x])
         act_new$steps[x]<-mpd$steps[y]
     }
}
```

The new data set created `act_new` consists of no NA values.

To get the total number of steps per day for the new set `act_new` we use the `aggregate` function and we assign that to the `spd_new` variable.
To get the mean of steps per interval for the new set `act_new` we use the `aggregate` function and we assign that to the `mpd_new` variable.


```r
spd_new<-aggregate(steps~date,data = act_new,sum,na.rm=TRUE)
mpd_new<-aggregate(steps~interval_new,data = act_new,mean,na.rm=TRUE)
```

To make the new histogram.


```r
hist(spd_new$steps,col = "green",main = "Histogram of steps taken per day",xlab = "Number of steps taken each day",breaks = 10)
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Taking the mean and median of the no. of steps per day for the new dataset.


```r
median_spd_new<-median(spd_new$steps)
mean_spd_new<-as.integer(mean(spd_new$steps))
median_spd_new
```

```
## [1] 10766.19
```

```r
mean_spd_new
```

```
## [1] 10766
```

```r
diff_mean<-mean_spd_new-mean_spd
diff_median<-median_spd_new-median_spd
```

These values are more from the previous ones by 0 in case of mean and 1.1886792 in case of median.

* When the NA values were not filled some days were skiped due to `na.rm` so previously the no. of observation were less but after filling the NA values the no. of days has increased. Since the new voids have been filled the days were there were NA values have been added but the days with no NA values are still the same.*

To tell whether the day is weekend or not we use `ifelse` function.


```r
act_new$end<-ifelse(as.POSIXlt(as.Date(act_new$date))$wday %% 6 == 0, "weekend", "weekday")
act_new$end<-factor(act_new$end,levels = c("weekday","weekend"))
```

To make the final panel plot.


```r
mpd_final <- aggregate(steps ~ interval + end, data = act_new, mean)
library(lattice)
xyplot(steps ~ interval | factor(end), data = mpd_final, layout = c(1,2), type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


