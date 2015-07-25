---
Title: "German Credit Data Analysis"
Author: "Srisai Sivakumar"
Date: "Friday, July 24, 2015"
Output: html_document
---

# German Credit Data Analysis

## By: Srisai Sivakumar

### Introduction

When a bank receives a loan application, based on the applicant's profile the bank has to make a decision regarding whether to go ahead with the loan approval or not. Two types of risks are associated with the bank's decision:

- If the applicant is a good credit risk, i.e. is likely to repay the loan, then not approving the loan to the person results in a loss of business to the bank

- If the applicant is a bad credit risk, i.e. is not likely to repay the loan, then approving the loan to the person results in a financial loss to the bank

It may be assumed that the second risk is a greater risk, as the bank (or any other institution lending the money to a untrustworthy party) had a higher chance of not being paid back the borrowed amount.

So its on the part of the bank or other lending authority to evaluate the risks associated with lending money to a customer.

This study aims at addressing this problem by using the applicant's demographic and socio-economic profiles to assess the risk of lending loan to the customer.

In business terms, we try to minimize the risk and maximize of profit for the bank. To minimize loss from the bank's perspective, the bank needs a decision rule regarding who to give approval of the loan and who not to. An applicant's demographic and socio-economic profiles are considered by loan managers before a decision is taken regarding his/her loan application.

The models used in thie study is not optimized for best performance. All models use the same 10-fold cross validation resampling option, without any manual tuning. Such manual tuning have been covered elsewhere in previous sections. Here we focus on the use of the penalty matrix as a loss function while building models.


### The data set

The German Credit data set is a publically available data set downloaded from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29)

All the details about the data is available in the above link. So we wont be describing the variables here.

The data contains data on 20 variables and the classification whether an applicant is considered a Good or a Bad credit risk for 1000 loan applicants.



```
## [1] "Lets look at the variables in the data set"
```

```
##  [1] "account.status"     "months"             "credit.history"    
##  [4] "purpose"            "credit.amount"      "savings"           
##  [7] "employment"         "installment.rate"   "personal.status"   
## [10] "guarantors"         "residence"          "property"          
## [13] "age"                "other.installments" "housing"           
## [16] "credit.cards"       "job"                "dependents"        
## [19] "phone"              "foreign.worker"     "credit.rating"
```

```
## [1] "Lets look at the structure of the data"
```

```
## 'data.frame':	1000 obs. of  21 variables:
##  $ account.status    : Factor w/ 4 levels "A11","A12","A13",..: 1 2 4 1 1 4 4 2 4 2 ...
##  $ months            : int  6 48 12 42 24 36 24 36 12 30 ...
##  $ credit.history    : Factor w/ 5 levels "A30","A31","A32",..: 5 3 5 3 4 3 3 3 3 5 ...
##  $ purpose           : Factor w/ 10 levels "A40","A41","A410",..: 5 5 8 4 1 8 4 2 5 1 ...
##  $ credit.amount     : int  1169 5951 2096 7882 4870 9055 2835 6948 3059 5234 ...
##  $ savings           : Factor w/ 5 levels "A61","A62","A63",..: 5 1 1 1 1 5 3 1 4 1 ...
##  $ employment        : Factor w/ 5 levels "A71","A72","A73",..: 5 3 4 4 3 3 5 3 4 1 ...
##  $ installment.rate  : int  4 2 2 2 3 2 3 2 2 4 ...
##  $ personal.status   : Factor w/ 4 levels "A91","A92","A93",..: 3 2 3 3 3 3 3 3 1 4 ...
##  $ guarantors        : Factor w/ 3 levels "A101","A102",..: 1 1 1 3 1 1 1 1 1 1 ...
##  $ residence         : int  4 2 3 4 4 4 4 2 4 2 ...
##  $ property          : Factor w/ 4 levels "A121","A122",..: 1 1 1 2 4 4 2 3 1 3 ...
##  $ age               : int  67 22 49 45 53 35 53 35 61 28 ...
##  $ other.installments: Factor w/ 3 levels "A141","A142",..: 3 3 3 3 3 3 3 3 3 3 ...
##  $ housing           : Factor w/ 3 levels "A151","A152",..: 2 2 2 3 3 3 2 1 2 2 ...
##  $ credit.cards      : int  2 1 1 1 2 1 1 1 1 2 ...
##  $ job               : Factor w/ 4 levels "A171","A172",..: 3 3 2 3 3 2 3 4 2 4 ...
##  $ dependents        : int  1 1 2 2 2 2 1 1 1 1 ...
##  $ phone             : Factor w/ 2 levels "A191","A192": 2 1 1 1 1 2 1 2 1 1 ...
##  $ foreign.worker    : Factor w/ 2 levels "A201","A202": 1 1 1 1 1 1 1 1 1 1 ...
##  $ credit.rating     : Factor w/ 2 levels "good","bad": 1 2 1 1 2 1 1 1 1 2 ...
```


### Exploratory Analysis

Now that we have the data, its important that we understand the data before we attempt to model it.

Lets look at some descriptive stats of some of some of the numeric variables like months, age and credit amount.


```
## 
## good  bad 
##  700  300
```

```
## 
## good  bad 
##  0.7  0.3
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     4.0    12.0    18.0    20.9    24.0    72.0
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   19.00   27.00   33.00   35.55   42.00   75.00
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     250    1366    2320    3271    3972   18420
```


Lets get a bit more statsy and look at cross-tabulation and test independence of the row and column variable.


```
## 
##  
##    Cell Contents
## |-------------------------|
## |                       N |
## |           N / Col Total |
## |-------------------------|
## 
##  
## Total Observations in Table:  1000 
## 
##  
##               | savings 
## credit.rating |       A61 |       A62 |       A63 |       A64 |       A65 | Row Total | 
## --------------|-----------|-----------|-----------|-----------|-----------|-----------|
##          good |       386 |        69 |        52 |        42 |       151 |       700 | 
##               |       0.6 |       0.7 |       0.8 |       0.9 |       0.8 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|-----------|
##           bad |       217 |        34 |        11 |         6 |        32 |       300 | 
##               |       0.4 |       0.3 |       0.2 |       0.1 |       0.2 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|-----------|
##  Column Total |       603 |       103 |        63 |        48 |       183 |      1000 | 
##               |       0.6 |       0.1 |       0.1 |       0.0 |       0.2 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|-----------|
## 
##  
## Statistics for All Table Factors
## 
## 
## Pearson's Chi-squared test 
## ------------------------------------------------------------
## Chi^2 =  36.09893     d.f. =  4     p =  2.761214e-07 
## 
## 
## 
```

```
## 
##  
##    Cell Contents
## |-------------------------|
## |                       N |
## |           N / Col Total |
## |-------------------------|
## 
##  
## Total Observations in Table:  1000 
## 
##  
##               | personal.status 
## credit.rating |       A91 |       A92 |       A93 |       A94 | Row Total | 
## --------------|-----------|-----------|-----------|-----------|-----------|
##          good |        30 |       201 |       402 |        67 |       700 | 
##               |       0.6 |       0.6 |       0.7 |       0.7 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|
##           bad |        20 |       109 |       146 |        25 |       300 | 
##               |       0.4 |       0.4 |       0.3 |       0.3 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|
##  Column Total |        50 |       310 |       548 |        92 |      1000 | 
##               |       0.0 |       0.3 |       0.5 |       0.1 |           | 
## --------------|-----------|-----------|-----------|-----------|-----------|
## 
##  
## Statistics for All Table Factors
## 
## 
## Pearson's Chi-squared test 
## ------------------------------------------------------------
## Chi^2 =  9.605214     d.f. =  3     p =  0.02223801 
## 
## 
## 
```

```
## 
##  
##    Cell Contents
## |-------------------------|
## |                       N |
## |           N / Col Total |
## |-------------------------|
## 
##  
## Total Observations in Table:  1000 
## 
##  
##               | dependents 
## credit.rating |         1 |         2 | Row Total | 
## --------------|-----------|-----------|-----------|
##          good |       591 |       109 |       700 | 
##               |       0.7 |       0.7 |           | 
## --------------|-----------|-----------|-----------|
##           bad |       254 |        46 |       300 | 
##               |       0.3 |       0.3 |           | 
## --------------|-----------|-----------|-----------|
##  Column Total |       845 |       155 |      1000 | 
##               |       0.8 |       0.2 |           | 
## --------------|-----------|-----------|-----------|
## 
##  
## Statistics for All Table Factors
## 
## 
## Pearson's Chi-squared test 
## ------------------------------------------------------------
## Chi^2 =  0.009089339     d.f. =  1     p =  0.9240463 
## 
## Pearson's Chi-squared test with Yates' continuity correction 
## ------------------------------------------------------------
## Chi^2 =  0     d.f. =  1     p =  1 
## 
## 
```

This reveals that there is dependence of savings and personal status on the credit rating. It also reveals that the number of dependents does not seem to have any bearing on the credit rating. Perhaps its fair to say that people who are intent on having a good credit rating continue to maintain the status irrespective of the number of dependents.

![plot of chunk stat.plots](figure/stat.plots-1.png) ![plot of chunk stat.plots](figure/stat.plots-2.png) ![plot of chunk stat.plots](figure/stat.plots-3.png) 


![plot of chunk relationplot](figure/relationplot-1.png) ![plot of chunk relationplot](figure/relationplot-2.png) ![plot of chunk relationplot](figure/relationplot-3.png) 

The first plot shows that the the most of the loans are sought to buy:

1. new car

2. furniture/equipment

3. radio/television

It also reveals that surprisingly few people buying used cars have bad rating! And not surprisingly, lower the age of the lonee and higher loan amount correlates to bad credits

The first obvious observation in the second plot is the absence of data for single women. Its not sure if its lack of data or there were no single women applying for loans- though the second possibility seems unlikely in real life. 

It reveal that single males tend to borrow more, and as before, younger they are and higher the loan amount corresponds to a bad rating.

The next most borrowing category is Female : divorced/separated/married.  The dominant trend in this category is smaller loan amount, higher the age, better the credit rating.

Males, married/widowed or divorced/separated have shown the least amount of borrowing. Because of this, its difficult to  visually observe any trends in these categories.

The histogram reveals that there is a right skewed nearly normal trend seen across all Personal Status and Sex categories, with 30 being the age where people in the sample seem to be borrowing the most.



### Models

Lets set up parallel processing.


```
## [1] "Number of registered cores is 4"
```

We use the caret package to split the data into training and test sets with a 70/30 split.


```
## 'data.frame':	700 obs. of  21 variables:
##  $ account.status    : Factor w/ 4 levels "A11","A12","A13",..: 1 1 4 2 1 2 1 4 1 2 ...
##  $ months            : int  6 24 36 30 48 12 24 24 30 24 ...
##  $ credit.history    : Factor w/ 5 levels "A30","A31","A32",..: 5 4 3 5 3 3 3 5 1 3 ...
##  $ purpose           : Factor w/ 10 levels "A40","A41","A410",..: 5 1 8 1 10 5 5 5 10 2 ...
##  $ credit.amount     : int  1169 4870 9055 5234 4308 1567 1282 2424 8072 12579 ...
##  $ savings           : Factor w/ 5 levels "A61","A62","A63",..: 5 1 5 1 1 1 2 5 5 1 ...
##  $ employment        : Factor w/ 5 levels "A71","A72","A73",..: 5 3 3 1 2 3 3 5 2 5 ...
##  $ installment.rate  : int  4 3 2 4 3 1 4 4 2 4 ...
##  $ personal.status   : Factor w/ 4 levels "A91","A92","A93",..: 3 3 3 4 2 2 2 3 3 2 ...
##  $ guarantors        : Factor w/ 3 levels "A101","A102",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ residence         : int  4 4 4 2 4 1 2 4 3 2 ...
##  $ property          : Factor w/ 4 levels "A121","A122",..: 1 4 4 3 2 3 3 2 3 4 ...
##  $ age               : int  67 53 35 28 24 22 32 53 25 44 ...
##  $ other.installments: Factor w/ 3 levels "A141","A142",..: 3 3 3 3 3 3 3 3 1 3 ...
##  $ housing           : Factor w/ 3 levels "A151","A152",..: 2 3 3 2 1 2 2 2 2 3 ...
##  $ credit.cards      : int  2 2 1 2 1 1 1 2 3 1 ...
##  $ job               : Factor w/ 4 levels "A171","A172",..: 3 3 2 4 3 3 2 3 3 4 ...
##  $ dependents        : int  1 2 2 1 1 1 1 1 1 1 ...
##  $ phone             : Factor w/ 2 levels "A191","A192": 2 1 2 1 1 2 1 1 1 2 ...
##  $ foreign.worker    : Factor w/ 2 levels "A201","A202": 1 1 1 1 1 1 1 1 1 1 ...
##  $ credit.rating     : Factor w/ 2 levels "good","bad": 1 2 1 2 2 1 2 1 1 2 ...
```

#### Penalty Matrix

An important aspect of most machine learning/predictive analytics problem this prediction accuracy. This problem too, is focussing on prediction accuracy. But thats not the only parameter we are concerned about. Recollect the objective of the study- 
1. minimize the risk, which would be ensured by good prediction accuracy
2. maximize the revenue to the bank, which is ensured by prompt repayment of the loans.

To achieve both, we not only have to ensure good prediction accuracy of good and bad credit ratings, we also need to minimize the mis-classification of good ratings as bad, and vice versa. It has to be noted that these 2 misclassifications DO NOT give same penalties. Its obvious that the bank stands more to lose if the customer with a bad rating is classified as a customer with a good rating, and given a loan that he/she defaults. This is more severe than classifying a good rating as bad, as it would make the bank miss only on the interest of the loan.

We achieve this goal by setting up a penalty matrix. Its shown below.

|         |           |           |           |
|---------|-----------|-----------|-----------|
|         | 1 = good  | Predicted | Predicted |
| 2 = bad | Penalties | 1         | 2         |
| Actual  | 1         | 0         | 1         |
| Actual  | 2         | 5         | 0         |

This means that we assign a penalty of 1 when we rate a customer as having bad credit raating, when infact the actual rating is good. This penalty signifies the loss of business to the bank.

We assign a much severe penalty of 5, when we misclassify a customer with bad rating, as having good rating. This is greater risk to the bank, hence higher penalty. To get the total penalty of a model, we mutiple each of the misclassification by the corresponding misclassification penalty score.

#### Baseline Models

As with any modelling task, we begin by defining the baseline model. Its with this model, we compare the models we are about to develop and establish improvements, if any.

The simplest prediction is taking all the loan applicants as having good credit rating. This would mean that we have about 70% accuracy. This doesnt mean anything without context. Lets compare this with the models we build and also with some literature.

This baseline would result in mis-classification that will be multiplied by the corresponding penalty score to get the total penalty of the model. Lets look at the accuracy and mean penalties of Baseline1 model


```
## [1] 0.7
```

```
##       
##        good
##   good  210
##   bad    90
```

```
## [1] "Baseline-1 model has an accuracy of 0.7 and a mean penalty of 3.5"
```

Without any other reference, its difficult to say if this accuracy and penalty is good or not good. But we can certainly say that all the penalties here are due to classifying the bad credits as good (as we have assumed all the credits are good for baseline 1)

Lets look at the accuracy and mean penalty for Baseline 2 model.


```
## [1] 0.3
```

```
##       
##        bad
##   good 210
##   bad   90
```

```
## [1] "Baseline-2 model has an accuracy of 0.3 and a mean penalty of 0.3"
```

This model does far worse in prediction accuracy, simultaneously much better in mean penalty. All the penalties in this model is due to classifying good credit ratings as bad and the resulting loff of business to the bank.


Before we build the models, lets define the important Penalty Matrix, that would form the backbone of the analysis.


```
##      [,1] [,2]
## [1,]    0    1
## [2,]    5    0
```

Lets build few tree and random forest models and compare them with these 2 baseline models.

#### Trees


```
## CART 
## 
## 700 samples
##  20 predictor
##   2 classes: 'good', 'bad' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 630, 630, 630, 630, 630, 630, ... 
## 
## Resampling results across tuning parameters:
## 
##   cp          Accuracy   Kappa      Accuracy SD  Kappa SD  
##   0.02380952  0.7442857  0.3381514  0.04638887   0.10330806
##   0.04285714  0.7571429  0.3603656  0.04096345   0.08950491
##   0.07142857  0.6985714  0.0634145  0.02376186   0.11545608
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was cp = 0.04285714.
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction good bad
##       good  187  23
##       bad    64  26
##                                           
##                Accuracy : 0.71            
##                  95% CI : (0.6551, 0.7607)
##     No Information Rate : 0.8367          
##     P-Value [Acc > NIR] : 1               
##                                           
##                   Kappa : 0.2062          
##  Mcnemar's Test P-Value : 1.799e-05       
##                                           
##             Sensitivity : 0.7450          
##             Specificity : 0.5306          
##          Pos Pred Value : 0.8905          
##          Neg Pred Value : 0.2889          
##              Prevalence : 0.8367          
##          Detection Rate : 0.6233          
##    Detection Prevalence : 0.7000          
##       Balanced Accuracy : 0.6378          
##                                           
##        'Positive' Class : good            
## 
```

```
## [1] "Tree model, tree, has an accuracy of 0.71 and a mean penalty of 1.14"
```


Lets add the penalty matrix into the tree model and see if that decreases the mean penalty.



```
## CART 
## 
## 700 samples
##  20 predictor
##   2 classes: 'good', 'bad' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 630, 630, 630, 630, 630, 630, ... 
## 
## Resampling results across tuning parameters:
## 
##   cp          Accuracy   Kappa      Accuracy SD  Kappa SD  
##   0.02380952  0.5771429  0.2453355  0.06142673   0.09161953
##   0.04285714  0.5700000  0.2352832  0.06440025   0.09247388
##   0.07142857  0.5828571  0.2433185  0.06346031   0.09797497
## 
## Accuracy was used to select the optimal model using  the smallest value.
## The final value used for the model was cp = 0.04285714.
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction good bad
##       good   91 119
##       bad    16  74
##                                           
##                Accuracy : 0.55            
##                  95% CI : (0.4918, 0.6072)
##     No Information Rate : 0.6433          
##     P-Value [Acc > NIR] : 0.9996          
##                                           
##                   Kappa : 0.1926          
##  Mcnemar's Test P-Value : <2e-16          
##                                           
##             Sensitivity : 0.8505          
##             Specificity : 0.3834          
##          Pos Pred Value : 0.4333          
##          Neg Pred Value : 0.8222          
##              Prevalence : 0.3567          
##          Detection Rate : 0.3033          
##    Detection Prevalence : 0.7000          
##       Balanced Accuracy : 0.6169          
##                                           
##        'Positive' Class : good            
## 
```

```
## [1] "Tree model,tree_p, has an accuracy of 0.55 and a mean penalty of 0.66"
```

We see that with the inclusion of the penalty matrix, the mean penalty has reduced.

#### Random Forest


```
## Random Forest 
## 
## 700 samples
##  20 predictor
##   2 classes: 'good', 'bad' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 630, 630, 630, 630, 630, 630, ... 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa      Accuracy SD  Kappa SD 
##    2    0.7500000  0.2888784  0.03642157   0.1055139
##   11    0.7657143  0.3918270  0.05137563   0.1319020
##   20    0.7585714  0.3842019  0.05148586   0.1285550
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 11.
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction good bad
##       good  187  23
##       bad    53  37
##                                           
##                Accuracy : 0.7467          
##                  95% CI : (0.6935, 0.7949)
##     No Information Rate : 0.8             
##     P-Value [Acc > NIR] : 0.9898977       
##                                           
##                   Kappa : 0.3333          
##  Mcnemar's Test P-Value : 0.0008794       
##                                           
##             Sensitivity : 0.7792          
##             Specificity : 0.6167          
##          Pos Pred Value : 0.8905          
##          Neg Pred Value : 0.4111          
##              Prevalence : 0.8000          
##          Detection Rate : 0.6233          
##    Detection Prevalence : 0.7000          
##       Balanced Accuracy : 0.6979          
##                                           
##        'Positive' Class : good            
## 
```

```
##       rf.pred
##        good bad
##   good  187  23
##   bad    53  37
```

```
## [1] "Random Forest model, rf, has an accuracy of 0.7467 and a mean penalty of 0.96"
```

Lets add the penalty matrix to the model


```
## Random Forest 
## 
## 700 samples
##  20 predictor
##   2 classes: 'good', 'bad' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 630, 630, 630, 630, 630, 630, ... 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa      Accuracy SD  Kappa SD  
##    2    0.7628571  0.3250823  0.03100728   0.08704387
##   11    0.7700000  0.4054470  0.04638887   0.12249075
##   20    0.7528571  0.3653013  0.05218578   0.13578427
## 
## Accuracy was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 20.
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction good bad
##       good  188  22
##       bad    52  38
##                                           
##                Accuracy : 0.7533          
##                  95% CI : (0.7005, 0.8011)
##     No Information Rate : 0.8             
##     P-Value [Acc > NIR] : 0.9798024       
##                                           
##                   Kappa : 0.3509          
##  Mcnemar's Test P-Value : 0.0007485       
##                                           
##             Sensitivity : 0.7833          
##             Specificity : 0.6333          
##          Pos Pred Value : 0.8952          
##          Neg Pred Value : 0.4222          
##              Prevalence : 0.8000          
##          Detection Rate : 0.6267          
##    Detection Prevalence : 0.7000          
##       Balanced Accuracy : 0.7083          
##                                           
##        'Positive' Class : good            
## 
```

```
##       rf_p.pred
##        good bad
##   good  188  22
##   bad    52  38
```

```
## [1] "Random Forest model, rf_p, has an accuracy of 0.7533 and a mean penalty of 0.94"
```

![plot of chunk rf1](figure/rf1-1.png) 

We see that with the inclusion of the penalty matrix, the mean penalty has gone down.

It can be noted that the most important predictor of the credit rating is account status, followed by months and credit history

### Results

We have the models we need, lets look at the results we have in hand. As we look at the models and its results, its important to analyze the results in the right context.

Besides accuracy and mean penalties, its important to have a metric that summarizes the effect of both accuracy and penalties. We can try the ratio of accuracy to penalty as the metric. But this skews the results. Instead, accuracy^2/penalty gives a better summary. So this will be used as the performance index metric for all the models developed above.



```
## [1] "Lets look at the accuracy of each model"
```

![plot of chunk results](figure/results-1.png) 

```
## [1] "Lets look at the mean penalty of each model"
```

![plot of chunk results](figure/results-2.png) 

```
## [1] "Lets look at the Performance Index of each model"
```

![plot of chunk results](figure/results-3.png) 

```
## [1] "Lets look at the accuracy-penalty summary of each model"
```

![plot of chunk results](figure/results-4.png) 

The penalties plot clearly shows the decrease of mean penalty. This affirms we have progressed in the right direction.

The model performance index plots also reveal that with better models, we have achieved better performance index values.

### Conclusion

We have modelled the German Credit Data set using naive and simple baseline models to random forest models. We have improved the from 0.7, to 0.76 with the r_f_p model. 

Results of the same data set available [elsewhere](http://bayesian-intelligence.com/publications/TR2010_1_zonneveldt_korb_nicholson_bn_class_credit_data.pdf) shows similar order of accuracies for prediction. Results from Applications of Data Mining in E-business and Finance, pp 28 also gives similar accuracies. So our model has similar prediction accuracies to the ones in literature.

We have also learnt how to apply penalty matrix to the tree and random forest models. We have seen that this reduces unfavourable mis-classifications


Footnote: If the file fails to get converted to html using the 'Knit HTML' option in R-Studio, we use the following lines of code to knit the file to html.
library(knitr)
knit2html("German.Rmd")
