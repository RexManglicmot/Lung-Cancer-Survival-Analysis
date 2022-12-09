Lung Cancer Survival Analysis
================

-   <a href="#status-continuing-working-document"
    id="toc-status-continuing-working-document">Status: Continuing Working
    Document</a>
-   <a href="#introduction" id="toc-introduction">Introduction</a>
-   <a href="#loading-the-libraries" id="toc-loading-the-libraries">Loading
    the Libraries</a>
-   <a href="#loading-the-data" id="toc-loading-the-data">Loading the
    Data</a>
-   <a href="#cleaning-the-data" id="toc-cleaning-the-data">Cleaning the
    Data</a>
-   <a href="#exploratory-data-analysis"
    id="toc-exploratory-data-analysis">Exploratory Data Analysis</a>
-   <a href="#survival-rate-analysis"
    id="toc-survival-rate-analysis">Survival Rate Analysis</a>
-   <a href="#limitations" id="toc-limitations">Limitations</a>
-   <a href="#conclusion" id="toc-conclusion">Conclusion</a>
-   <a href="#appendix" id="toc-appendix">Appendix</a>

### Status: Continuing Working Document

Hi everyone. I’m continuing building my data analysis and R skills. As
such, I would love feedback to better improve this project via
<rexmanglicmot@gmail.com>. Any mistakes and misrepresentation of the
data are my own.

Things Need To DO:

1.  More Theory of Survival Analysis
2.  Create a dumbbell chart
3.  Limitations
4.  Check Grammar
5.  More info on Lung Cancer and the NCCTG
6.  General Statistics about lung cancer and WHY it is important to
    investigate
7.  Create some cool looking plots
8.  Get Feedback

### Introduction

![](https://www.ilcn.org/wp-content/uploads/2021/12/142.jpg)

Lung Cancer is the leading cause of most cancer deaths, taking almost up
to 25%.[^1]Further, it is the leading cause of cancer in both men and
females. [^2] According to a 2019 US statistic, it is the leading cause
of cancer behind female breast cancer and male prostate cancer.[^3]

Survival Rate is a method in which we can measure the time of a an event
from the start to finish.

By studying the survival rate of lung cancer patients, local and state
governments are able to educate the population for the impending problem
they can likely face. By understanding surivival rates,

This project will organized in the following chapters:

1.  Loading the Libraries
2.  Loading the Data
3.  Cleaning the Data
4.  Exploratory Data Analysis
5.  Survival Analysis
6.  Limitations
7.  Conclusion
8.  Appendix
9.  Inspiration for this project

A special acknowledgement to Loprinzo CL. et al.[^4] for their lung
cancer research and also in providing this dataset in the survival
package.

The raw dataset attributes are:

1.  inst: Institution code
2.  time: Survival time in days
3.  status: censoring status 1=censored, 2=dead
4.  age: Age in years
5.  sex: Male=1 Female=2
6.  ph.ecog: ECOG performance score as rated by the physician.
    0=asymptomatic, 1= symptomatic but completely ambulatory, 2= in bed
    \<50% of the day, 3= in bed \> 50% of the day but not bedbound, 4 =
    bedbound
7.  ph.karno: Karnofsky performance score (bad=0-good=100) rated by
    physician
8.  pat.karno: Karnofsky performance score as rated by patient
9.  meal.cal: Calories consumed at meals
10. wt.loss: Weight loss in last six months (pounds)

### Loading the Libraries

``` r
#install packages
#install.packages('survival') #installed on 12/3/22
#install.packages('ggpubr') installed on 12/3/22
#install.packages('skimr') #installed on 12/4/22

library(survival)
library(tidyverse)
library(ggpubr)
library(skimr)
library(ggpubr)
```

### Loading the Data

``` r
#view the names of the dataset in Survival package
print(data(package = "survival"))
```

So we see that the package has a bunch of datasets we can work with. So,
let’s choose the cancer dataset and view it. Let’s store the dataset
into a new object, data, for data cleaning.

``` r
#see the structure of the dataset
str(cancer)
```

    ## 'data.frame':    228 obs. of  10 variables:
    ##  $ inst     : num  3 3 3 5 1 12 7 11 1 7 ...
    ##  $ time     : num  306 455 1010 210 883 ...
    ##  $ status   : num  2 2 1 2 2 1 2 2 2 2 ...
    ##  $ age      : num  74 68 56 57 60 74 68 71 53 61 ...
    ##  $ sex      : num  1 1 1 1 1 1 2 2 1 1 ...
    ##  $ ph.ecog  : num  1 0 0 1 0 1 2 2 1 2 ...
    ##  $ ph.karno : num  90 90 90 90 100 50 70 60 70 70 ...
    ##  $ pat.karno: num  100 90 90 60 90 80 60 80 80 70 ...
    ##  $ meal.cal : num  1175 1225 NA 1150 NA ...
    ##  $ wt.loss  : num  NA 15 15 11 0 0 10 1 16 34 ...

``` r
#provides more info about the dataset and the variables, which allows for
#better data cleaning
?cancer

#store cancer dataset into a new object for data manipulation
data <- cancer
```

### Cleaning the Data

There are a couple tasks that needs to be done.

First, we need to delete the number of NA values. Because each
observation is an individual, it is best to delete the obersvation to
get the dataset tidy. It is also not best to find take the average of
some sort unlike business data (i.e. monthly profit).

Second, we need to convert the variables into appropoate classes based
on the data description in ?cancer. For example the variable status is a
number and needs to convereted to a factor.

``` r
#Count the number of NAs in the dataframe
sum((is.na(data)))
```

    ## [1] 67

We have 67 NA values within out dataset. Let’s use another method to
locate the speicific row and location where we might find these values.

``` r
#find out specific observations in which there are NAs
which(is.na(data), arr.ind=TRUE)
```

    ##     row col
    ## 156 156   1
    ## 14   14   6
    ## 206 206   7
    ## 67   67   8
    ## 79   79   8
    ## 105 105   8
    ## 3     3   9
    ## 5     5   9
    ## 12   12   9
    ## 13   13   9
    ## 16   16   9
    ## 23   23   9
    ## 25   25   9
    ## 33   33   9
    ## 44   44   9
    ## 51   51   9
    ## 52   52   9
    ## 58   58   9
    ## 74   74   9
    ## 92   92   9
    ## 95   95   9
    ## 97   97   9
    ## 98   98   9
    ## 100 100   9
    ## 103 103   9
    ## 104 104   9
    ## 105 105   9
    ## 114 114   9
    ## 115 115   9
    ## 121 121   9
    ## 130 130   9
    ## 132 132   9
    ## 134 134   9
    ## 137 137   9
    ## 146 146   9
    ## 150 150   9
    ## 152 152   9
    ## 153 153   9
    ## 154 154   9
    ## 157 157   9
    ## 167 167   9
    ## 178 178   9
    ## 184 184   9
    ## 198 198   9
    ## 206 206   9
    ## 207 207   9
    ## 209 209   9
    ## 210 210   9
    ## 214 214   9
    ## 215 215   9
    ## 217 217   9
    ## 223 223   9
    ## 224 224   9
    ## 1     1  10
    ## 20   20  10
    ## 36   36  10
    ## 44   44  10
    ## 56   56  10
    ## 63   63  10
    ## 108 108  10
    ## 138 138  10
    ## 178 178  10
    ## 183 183  10
    ## 192 192  10
    ## 193 193  10
    ## 206 206  10
    ## 209 209  10

Looking at the output we see a good amount in the dougle digits as well
as the triple digits (100 and 200s). Interestingly though, we see that
many of the NA values are in the 9th and 10 rows, the meal.cal and
wt.loss variables. Let’s count the number of NAs in each variable.

``` r
#count the number of NAs within each column
colSums(is.na(data))
```

    ##      inst      time    status       age       sex   ph.ecog  ph.karno pat.karno 
    ##         1         0         0         0         0         1         1         3 
    ##  meal.cal   wt.loss 
    ##        47        14

This is interesting. We see that majority of the NAs are in columns are
in meal.cal and wt.loss. The most important variables for the survival
analysis are time and status which have no NAs. Much data analysis is
usually done with complete datasets, so in our case, I will delete rows
that contain NAs and work wit that data set.

Nonetheless, it would interesting to see if the survival analysis would
significantly change. So, I will plot 2: one with the complete data set
and the other with the tidy data set, data2.

``` r
#delete all the rows with NAs
data2 <- na.omit(data)

#count the number of NAs within each column
colSums(is.na(data2))
```

    ##      inst      time    status       age       sex   ph.ecog  ph.karno pat.karno 
    ##         0         0         0         0         0         0         0         0 
    ##  meal.cal   wt.loss 
    ##         0         0

``` r
#creat list of variable names into a object
names <- c('status', 'sex', 'ph.ecog')
  
#convert some of the variabes to appropiate classes using 1 line of code
data2[names] <- lapply(data2[names], factor)

#check str again
sapply(data2, class)
```

    ##      inst      time    status       age       sex   ph.ecog  ph.karno pat.karno 
    ## "numeric" "numeric"  "factor" "numeric"  "factor"  "factor" "numeric" "numeric" 
    ##  meal.cal   wt.loss 
    ## "numeric" "numeric"

``` r
#get rid of irrelevant coumns
data2 <- data2[,-1]
```

Great! We have correctly cleaned out data and now let’s move on the the
best part, EDA!

### Exploratory Data Analysis

Let’s review some of the basic summary statistics via a new function,
skimr.

``` r
#a function to call summary statistics
skim(data2)
```

|                                                  |       |
|:-------------------------------------------------|:------|
| Name                                             | data2 |
| Number of rows                                   | 167   |
| Number of columns                                | 9     |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |       |
| Column type frequency:                           |       |
| factor                                           | 3     |
| numeric                                          | 6     |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |       |
| Group variables                                  | None  |

Data summary

**Variable type: factor**

| skim_variable | n_missing | complete_rate | ordered | n_unique | top_counts                |
|:--------------|----------:|--------------:|:--------|---------:|:--------------------------|
| status        |         0 |             1 | FALSE   |        2 | 2: 120, 1: 47             |
| sex           |         0 |             1 | FALSE   |        2 | 1: 103, 2: 64             |
| ph.ecog       |         0 |             1 | FALSE   |        4 | 1: 81, 0: 47, 2: 38, 3: 1 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |   mean |     sd |  p0 |   p25 | p50 |    p75 | p100 | hist  |
|:--------------|----------:|--------------:|-------:|-------:|----:|------:|----:|-------:|-----:|:------|
| time          |         0 |             1 | 309.93 | 209.44 |   5 | 174.5 | 268 |  419.5 | 1022 | ▇▇▃▂▁ |
| age           |         0 |             1 |  62.57 |   9.21 |  39 |  57.0 |  64 |   70.0 |   82 | ▂▅▇▇▃ |
| ph.karno      |         0 |             1 |  82.04 |  12.78 |  50 |  70.0 |  80 |   90.0 |  100 | ▃▃▇▇▅ |
| pat.karno     |         0 |             1 |  79.58 |  15.10 |  30 |  70.0 |  80 |   90.0 |  100 | ▁▁▆▅▇ |
| meal.cal      |         0 |             1 | 929.13 | 413.49 |  96 | 619.0 | 975 | 1162.5 | 2600 | ▅▇▅▁▁ |
| wt.loss       |         0 |             1 |   9.72 |  13.38 | -24 |   0.0 |   7 |   15.0 |   68 | ▁▇▃▁▁ |

With this function, we can see that the summary statistics of all the
variables. The mean age is 63 years old, total meal calories is 929 and
the weight loss is 9.8 pounds.

Although the mean age is 63, let’s split this into men and women.

This is interesting because both men and women have different calorie
intake because the naturally difference in body composition. However,
when diagnosed with cancer this could change So in the next steps, lets
plot those distribution.

Another interesting thing is the weight loss. It would be interested to
see how their calories differ as well as the weight loss. So let’s plot
those.

\#Lastly, the ph.karno and pat.karno are reported scores by the physican
and patient. It would be interesting to see if \#there is an
relationship between the two.

``` r
#histogram of men and women ages
ggplot(data2, aes(x=age, group=sex, fill=sex)) +
  geom_histogram(bins = 20, color= 'black') +
  scale_fill_brewer(palette="Dark2") +
  facet_wrap(~sex) +
  theme_minimal()
```

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Based on this, we see that there males have a symmetric distribution
whereas the female is right-skewed with a few outliers to the left.

``` r
ggplot(data2, aes(sex, meal.cal)) +
  geom_hex(bins = 20, color = "white")+
  #NEED TO CHANGE COLORS
  scale_fill_gradient(low =  "#00AFBB", high = "#FC4E07")+
  theme_minimal()
```

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
ggplot(data2, aes(x = sex, y= meal.cal, fill = sex)) +
 geom_dotplot(binaxis='y', stackdir='center') +
  scale_fill_brewer(palette="Dark2") +
  theme_minimal()
```

    ## Bin width defaults to 1/30 of the range of the data. Pick better value with
    ## `binwidth`.

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-12-2.png)<!-- -->
Because we are working with a small dataset, we can use a dotplot to see
the number of calories both sexes had during the experiment. It seems
they had roughly \~1000 calrories for both groups. (Boxplot is in the
appendix).

``` r
data2 %>%
  group_by(sex) %>%
  summarize(mean(meal.cal))
```

    ## # A tibble: 2 × 2
    ##   sex   `mean(meal.cal)`
    ##   <fct>            <dbl>
    ## 1 1                 985.
    ## 2 2                 840.

We see that the males have a slight higher meal calories than the
females.

``` r
ggplot(data2, aes(x=time, y=wt.loss, fill=sex)) +
  geom_point() +
  geom_smooth() +
  facet_wrap(~sex) +
  theme_minimal()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
ggplot(data2, aes(x=age, y=wt.loss)) +
  geom_point() +
  geom_smooth(linetype="dashed",
             color="orange", fill="lightpink") +
  theme_bw()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Now that we have explored the data. Let’s go more into the theory of
survial analysis.

### Survival Rate Analysis

<img
src="https://www.ncss.com/wp-content/uploads/2012/10/Life-Table-Analysis-Survival-Plot.png"
style="width:75.0%" />

Survival Analysis (SA) is the studying of the time between the entry of
a study to its subsequent event. SA can be broken down into Survival and
Time measures.

In terms of survival measures, it has traditionally been termed for
studies regarding health outcomes, i.e., the time a person starts a drug
treatment to their death, the time it takes for a durg addict to
resrtart taking drugs after leaving a drug rehab center. However, it has
also been used for business purposes, such as the amount of time it
takes for a customer to buy a product once an email with a coupon is
delivered to them or the time. Therefore, SA is used for a wide vareity
of applications across a plethora of industries. In terms of time
measures, it can be mesaured by days, weeks, years, etc. depending on
the design of the experiment.

Looking at the graph above, it can start at the top of the y-axis
(survival measure) and decreases downward as the line moves across the
x-axis (time).

Censoring is an important concept in SA and occurs

### Limitations

### Conclusion

Will complete this part when the above sections are complete.

### Appendix

``` r
ggplot(data2, aes(x=meal.cal, y=wt.loss)) +
  geom_point() +
  geom_smooth(linetype="dashed",
             color="darkred", fill="lightblue") +
  theme_bw()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
ggplot(data2, aes(sex, meal.cal, fill= sex)) +
  geom_boxplot() +
  scale_fill_brewer(palette="Dark2") +
  theme_minimal()
```

![](Lung-Cancer-Survival-Analysis_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

[^1]: <https://www.cancer.org/cancer/lung-cancer/about/key-statistics.html>

[^2]: <https://www.lung.org/lung-health-diseases/lung-disease-lookup/lung-cancer/resource-library/lung-cancer-fact-sheet>

[^3]: <https://gis.cdc.gov/Cancer/USCS/#/AtAGlance/>

[^4]: Loprinzi CL. Laurie JA. Wieand HS. Krook JE. Novotny PJ. Kugler
    JW. Bartel J. Law M. Bateman M. Klatt NE. et al. Prospective
    evaluation of prognostic variables from patient-completed
    questionnaires. North Central Cancer Treatment Group. Journal of
    Clinical Oncology. 12(3):601-7, 1994.
