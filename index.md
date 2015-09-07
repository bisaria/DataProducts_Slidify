---
title       : Developing Data Product - Slidify
subtitle    : 10-Year Heart Attack Risk Assessment
author      : R Bisaria
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax, quiz, bootstrap]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

### `The Framingham Heart Study`


The Framingham Heart Study is considered to be one of the most important epidemiological studies ever conducted. Its underlying analytics have had huge impact on our understanding of cardiovascular disease.

A better understanding of the risk factors that increase the chance of cardiovascular disease can be a key to successful prediction and prevention of this disease.

 * Demograhic risk factor : age, sex, education etc.
 * Behavioral Risk factor : Smokes, cigarettes per day etc.
 * Medical risk factors : Blood pressure, diabetes etc.

The study started in 1948 and followed 5209 patients, aged 30-59, for a number of years with every 2 yearly collection of data based on an extensive questionnaire, which was continuosly evaluated and expanded over time. It collected and documented patients' physical, behavioral and medical test data. 

[The Framingham Heart Study data](https://biolincc.nhlbi.nih.gov/static/studies/teaching/framdoc.pdf) has since been used to predict and prevent heart disease.

--- .class #id 

### `Analytics to Prevent Heart Disease`

 - Read dataset from csv file
 



 - Randomly split data into training and test set



```r
split = sample.split(framingham$TenYearCHD, SplitRatio = 0.7)
train = subset(framingham, split==TRUE)
test = subset(framingham, split==FALSE)
```

 - Identify `Risk Factors` using training data using logistic regression
  

```r
framinghamLog = glm(TenYearCHD ~ ., data = train, family=binomial)
summary(framinghamLog)
```

 - Create model for prediction using only the significant variables. 
 

```r
framinghamLog = glm(TenYearCHD ~ male+age+currentSmoker+BPMeds+totChol+sysBP, 
                    data = framingham, 
                    family=binomial)
```
 

--- .class #id

### `Analytics to Prevent Heart Disease (contd.)`
 - Validate model using test data
 

```r
predictTest = predict(framinghamLog, type="response", newdata=test)
table(test$TenYearCHD, predictTest > 0.5)   # Confusion matrix with threshold of 0.5
```

```
##    
##     FALSE TRUE
##   0  1050    5
##   1   177    6
```
 - Accuracy: 

```
## [1] 0.8301887
```
 - AUC: 

```
## [1] 0.7341828
```

As these values are reasonaly good, we can go ahead and try prediction.

--- .class #id

### `10-Year Heart Attack Risk Assessment App` 

[This app](https://bisaria.shinyapps.io/DataProducts_PeerAssessment) tries to predict 10-year heart attack risk using data from Framingham Heart Study. 

  The user is required to enter the following information:

  * Age
  * Gender
  * Total Cholestrol Level in mg/dL
  * Systolic Blood Pressure, which is the first number from a BP reading.
  * Does he smoke?
  * Is he under any BP medication?
  
Based on these informations, the app tries to predict if the user has any risk of developing heart disease in 10 years time. If the probabilty of risk is more than 50%, the app generates a warning.

Disclaimer: This app has been prepared only for academic purposes.
