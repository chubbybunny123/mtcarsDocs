---
title       : Estimating
subtitle    : from a Linear Regression of the mtcars dataset 
author      : chubbybunny123
job         : 
framework   : io2012    # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
--- 

## Motivation


- See how changes in characteristics effect fuel economy  
- Input your own car's characteristics to compare it to 
cars from 1973-74!  

<center>
<img src="./images/cars_combined.png" alt="Car 1" style="height: 300px;"/>
</center>


Car designed by [Andrew Fortnum](http://www.thenounproject.com/AndrewFortnum), Smart Car designed by [Simon Child](http://www.thenounproject.com/Simon Child), Gas designed by [Jon Testa](http://www.thenounproject.com/jontesta), Beaker designed by [Edward Boatman](http://www.thenounproject.com/edward), all from the [Noun Project](http://www.thenounproject.com)  

---

## App features

1. Interactive sliders and radio buttons to input car characteristics  
2. Automatically updating graph to show your selection amongst the training data  
3. Estimated fuel economy calculated

<center>
<img src="./images/app_screenshot.png" alt="app_screen" style="height: 450px;"/>
</center>


---

## Model anatomy

- mtcars data split into training and testing groups
    - Use testing group to get an out-of-sample error estimate
- Linear regression applied to training group


```r
set.seed(38951)
library(datasets); data(mtcars)
inTrain <- sample(1:length(mtcars$mpg), size=floor(0.6*length(mtcars$mpg)), replace=FALSE)
training <- mtcars[inTrain,]; testing <- mtcars[-inTrain,]
modfit <- lm(mpg ~ wt + factor(cyl) + factor(am), data = training)
summary(modfit)$coef
```

```
##              Estimate Std. Error t value  Pr(>|t|)
## (Intercept)   33.4422      3.567  9.3746 2.066e-07
## wt            -3.3332      1.109 -3.0052 9.454e-03
## factor(cyl)6  -3.5224      2.569 -1.3713 1.919e-01
## factor(cyl)8  -4.5034      2.242 -2.0087 6.426e-02
## factor(am)1    0.2383      2.001  0.1191 9.069e-01
```

---

## Assumptions made/Variables neglected

- Used only cyl (# cylinders), am (transmission type), and wt (weight) in the model
- Did not include disp, hp, drat, qsec, vs, gear, carb as variables because their inclusion should not significantly change model


```r
an2 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+qsec, data = training))
an3 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+hp, data = training))
an4 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+gear, data = training))
an5 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+disp, data = training))
an6 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+carb, data = training))
an7 <- anova(modfit, lm(mpg~wt+factor(cyl)+factor(am)+drat, data = training))
data.frame('qsec'=round(an2$Pr[2],3), 'hp'=round(an3$Pr[2],3)
           , 'gear'=round(an4$Pr[2],3), 'disp'=round(an5$Pr[2],3)
           , 'carb'=round(an6$Pr[2],3), 'drat'=round(an7$Pr[2],3))
```

```
##    qsec  hp  gear disp  carb  drat
## 1 0.293 0.2 0.859 0.54 0.487 0.106
```



