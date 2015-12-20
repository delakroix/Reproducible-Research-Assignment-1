---
title: "Coursera Assignment"
author: "Yihao"
date: "18 December 2015"
output: html_document
-----


Introduction
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, orJawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.
This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

Data
The data for this assignment can be downloaded from the course web site:
.	Dataset: Activity monitoring data [52K]
The variables included in this dataset are:
.	steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)
.	date: The date on which the measurement was taken in YYYY-MM-DD format
.	interval: Identifier for the 5-minute interval in which measurement was taken
The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.
Assignment
This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a single R markdown document that can be processed by knitr and be transformed into an HTML file.
Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use echo = TRUE so that someone else will be able to read the code. This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis.
For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)
Fork/clone the GitHub repository created for this assignment. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.
NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.

Loading and preprocessing the data

Show any code that is needed to
1.	Load the data (i.e. read.csv())
Download the zip file and unzip the activity.csv into the working directory


```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```


Loading the data.

```r
sportsdata <- read.csv("C:/Users/yihaon/Documents/activity.csv")
```


What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.
1.	Calculate the total number of steps taken per day


```r
totalsteps <- sum(sportsdata$steps, na.rm = TRUE)
totalsteps
```

```
## [1] 570608
```


If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

Use histogram function

```r
tseachday <- aggregate(steps~date, data=sportsdata, FUN=sum, na.rm=TRUE)
hist(tseachday$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 


Calculate and report the mean and median of the total number of steps taken per day:

```r
tsmean <- mean(tseachday$steps)
tsmedian <- median(tseachday$steps)
tsmean
```

```
## [1] 10766.19
```

```r
tsmedian
```

```
## [1] 10765
```


What is the average daily activity pattern?
1.	Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

Time series plot done using mean of 5-min interval and the plot function:

```r
fiveminavg <- aggregate(steps~interval, data=sportsdata, FUN=mean, na.rm=TRUE)
plot(x = fiveminavg$interval, y = fiveminavg$steps, type = "l") 
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

2.	Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

Set found using max and for loop function:

```r
maxsteps <- max(fiveminavg$steps)
for (i in 1:288) 
{
    if (fiveminavg$steps[i] == maxsteps)
        FM_Int_MS <- fiveminavg$interval[i]
}
FM_Int_MS
```

```
## [1] 835
```


Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.


1.	Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

Rows with missing values is as below:

```r
NArows <- sum(is.na(sportsdata$steps))
NArows
```

```
## [1] 2304
```

2.	Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
sportsdata2<- sportsdata %>%
  group_by(interval)  %>%
  mutate(steps= ifelse(is.na(steps), mean(steps, na.rm=TRUE), steps))
```


3.	Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
sportsdata3<- sportsdata2 %>%
  group_by(date) %>%
  summarize(total.steps=sum(steps, na.rm=TRUE))
```


4.	Make a histogram of the total number of steps taken each day 


```r
tseachday2 <- aggregate(steps~date, sportsdata2, FUN=sum, na.rm=TRUE)

hist(tseachday2$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 


Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment?


```r
tsmean2 <- mean(tseachday2$steps)
tsmedian2 <- median(tseachday2$steps)

tsmean2 #no change
```

```
## [1] 10766.19
```

```r
tsmedian2 #yes, it changed
```

```
## [1] 10766.19
```


For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
1.	Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

2.	Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
week <- wday(sportsdata2$date)
weekday <- week
for (i in 1:17568) # loop to find the na
{
    if(week[i] == 1)
        weekday[i] <- 'WE'
    if(week[i] == 2)
        weekday[i] <- 'WD'
    if(week[i] == 3)
        weekday[i] <- 'WD'
    if(week[i] == 4)
        weekday[i] <- 'WD'
    if(week[i] == 5)
        weekday[i] <- 'WD'
    if(week[i] == 6)
        weekday[i] <- 'WD'
    if(week[i] == 7)
        weekday[i] <- 'WE'
}

sportsdata2$weekday <-weekday

weekday <- grep("WD",sportsdata2$weekday)
weekday_frame <- sportsdata2[weekday,]
weekend_frame <- sportsdata2[-weekday,]
    
fiveminavgwd <- aggregate(steps~interval,data=weekday_frame, FUN=mean, na.rm=TRUE)
fiveminavgwe <- aggregate(steps~interval, data=weekend_frame, FUN=mean, na.rm=TRUE)

plot(x = fiveminavgwd$interval, y = fiveminavgwd$steps, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png) 

```r
plot(x = fiveminavgwe$interval, y = fiveminavgwe$steps, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-2.png) 




Submitting the Assignment
To submit the assignment:
1.	Commit the your completed PA1_template.Rmd file to the master branch of your git repository (you should already be on the master branch unless you created new ones)
2.	Commit your PA1_template.md and PA1_template.html files produced by processing your R markdown file with knit2html() function in R (from the knitr package) by running the function from the console.
3.	If your document has figures included (it should) then they should have been placed in the figure/ directory by default (unless you overrided the default). Add and commit the figure/ directory to yoru git repository so that the figures appear in the markdown file when it displays on github.
4.	Push your master branch to GitHub.
5.	Submit the URL to your GitHub repository for this assignment on the course web site.
In addition to submitting the URL for your GitHub repository, you will need to submit the 40 character SHA-1 hash (as string of numbers from 0-9 and letters from a-f) that identifies the repository commit that contains the version of the files you want to submit. You can do this in GitHub by doing the following
1.	Going to your GitHub repository web page for this assignment
2.	Click on the "?? commits" link where ?? is the number of commits you have in the repository. For example, if you made a total of 10 commits to this repository, the link should say "10 commits".
3.	You will see a list of commits that you have made to this repository. The most recent commit is at the very top. If this represents the version of the files you want to submit, then just click the "copy to clipboard" button on the right hand side that should appear when you hover over the SHA-1 hash. Paste this SHA-1 hash into the course web site when you submit your assignment. If you don't want to use the most recent commit, then go down and find the commit you want and copy the SHA-1 hash.
A valid submission will look something like (this is just an example!)
https://github.com/rdpeng/RepData_PeerAssessment1


