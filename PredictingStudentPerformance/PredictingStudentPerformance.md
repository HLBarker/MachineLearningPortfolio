Predicting student performance
================

Objective
---------

Accurately predict a student's performance (final grade) in math from various attributes (e.g., family size, age, weekly study time, health, etc.) about the student.

Data
----

The dataset includes the student's final math score (performance) and 30 different characteristics about the student and is freely available on the [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/datasets/Student+Performance).

``` r
data <- read.csv("~/Documents/Data Science/GitHub_repository/MachineLearningPortfolio/PredictingStudentPerformance/student_data/student-mat.csv", sep = ";", header = TRUE) # student math scores and characteristics

str(data) # 395 students with 33 variables, "G3.y" is the final grade and predictor target
```

    ## 'data.frame':    395 obs. of  33 variables:
    ##  $ school    : Factor w/ 2 levels "GP","MS": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ sex       : Factor w/ 2 levels "F","M": 1 1 1 1 1 2 2 1 2 2 ...
    ##  $ age       : int  18 17 15 15 16 16 16 17 15 15 ...
    ##  $ address   : Factor w/ 2 levels "R","U": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ famsize   : Factor w/ 2 levels "GT3","LE3": 1 1 2 1 1 2 2 1 2 1 ...
    ##  $ Pstatus   : Factor w/ 2 levels "A","T": 1 2 2 2 2 2 2 1 1 2 ...
    ##  $ Medu      : int  4 1 1 4 3 4 2 4 3 3 ...
    ##  $ Fedu      : int  4 1 1 2 3 3 2 4 2 4 ...
    ##  $ Mjob      : Factor w/ 5 levels "at_home","health",..: 1 1 1 2 3 4 3 3 4 3 ...
    ##  $ Fjob      : Factor w/ 5 levels "at_home","health",..: 5 3 3 4 3 3 3 5 3 3 ...
    ##  $ reason    : Factor w/ 4 levels "course","home",..: 1 1 3 2 2 4 2 2 2 2 ...
    ##  $ guardian  : Factor w/ 3 levels "father","mother",..: 2 1 2 2 1 2 2 2 2 2 ...
    ##  $ traveltime: int  2 1 1 1 1 1 1 2 1 1 ...
    ##  $ studytime : int  2 2 2 3 2 2 2 2 2 2 ...
    ##  $ failures  : int  0 0 3 0 0 0 0 0 0 0 ...
    ##  $ schoolsup : Factor w/ 2 levels "no","yes": 2 1 2 1 1 1 1 2 1 1 ...
    ##  $ famsup    : Factor w/ 2 levels "no","yes": 1 2 1 2 2 2 1 2 2 2 ...
    ##  $ paid      : Factor w/ 2 levels "no","yes": 1 1 2 2 2 2 1 1 2 2 ...
    ##  $ activities: Factor w/ 2 levels "no","yes": 1 1 1 2 1 2 1 1 1 2 ...
    ##  $ nursery   : Factor w/ 2 levels "no","yes": 2 1 2 2 2 2 2 2 2 2 ...
    ##  $ higher    : Factor w/ 2 levels "no","yes": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ internet  : Factor w/ 2 levels "no","yes": 1 2 2 2 1 2 2 1 2 2 ...
    ##  $ romantic  : Factor w/ 2 levels "no","yes": 1 1 1 2 1 1 1 1 1 1 ...
    ##  $ famrel    : int  4 5 4 3 4 5 4 4 4 5 ...
    ##  $ freetime  : int  3 3 3 2 3 4 4 1 2 5 ...
    ##  $ goout     : int  4 3 2 2 2 2 4 4 2 1 ...
    ##  $ Dalc      : int  1 1 2 1 1 1 1 1 1 1 ...
    ##  $ Walc      : int  1 1 3 1 2 2 1 1 1 1 ...
    ##  $ health    : int  3 3 3 5 5 5 3 1 1 5 ...
    ##  $ absences  : int  6 4 10 2 4 10 0 6 0 0 ...
    ##  $ G1        : int  5 5 7 15 6 15 12 6 16 14 ...
    ##  $ G2        : int  6 5 8 14 10 15 12 5 18 15 ...
    ##  $ G3        : int  6 6 10 15 10 15 11 6 19 15 ...

``` r
data %>% 
  sapply(function(x) sum(is.na(x))) # no missing data
```

    ##     school        sex        age    address    famsize    Pstatus 
    ##          0          0          0          0          0          0 
    ##       Medu       Fedu       Mjob       Fjob     reason   guardian 
    ##          0          0          0          0          0          0 
    ## traveltime  studytime   failures  schoolsup     famsup       paid 
    ##          0          0          0          0          0          0 
    ## activities    nursery     higher   internet   romantic     famrel 
    ##          0          0          0          0          0          0 
    ##   freetime      goout       Dalc       Walc     health   absences 
    ##          0          0          0          0          0          0 
    ##         G1         G2         G3 
    ##          0          0          0

``` r
data %>% 
  boxplot(use.cols = TRUE) # a few fairly skewed predictors and variable scales
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/load%20+%20explore%20data-1.png)

Data transformation
-------------------

After trying out various transformations (including centering, scaling, normalization, BoxCox, and YeoJohnson), I have decided to use standardization which helps to make the data more normal in distribution while still maintaining variation in the predictors.

``` r
preprocessParams <- preProcess(data, method = c("center", "scale")) # calculate the pre-process parameters from the dataset by standardizing them
print(preprocessParams) # summarize transform parameters
```

    ## Created from 395 samples and 33 variables
    ## 
    ## Pre-processing:
    ##   - centered (16)
    ##   - ignored (17)
    ##   - scaled (16)

``` r
transformed <- predict(preprocessParams, data) # transform the dataset using the parameters
# summary(transformed) # summarize the transformed dataset
boxplot(transformed, use.cols = TRUE)
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/data%20transformation-1.png)

Remove redundant features
-------------------------

``` r
nums <- sapply(transformed, is.numeric)
transformed_numeric <- transformed[ , nums]

factors <- sapply(transformed, is.factor)
transformed_factors <- transformed[ , factors]

correlationMatrix <- cor(transformed_numeric)
highlyCorrelated <- findCorrelation(correlationMatrix, cutoff = 0.75)
print(highlyCorrelated) # remove attributes with an absolute correlation of >= 0.75
```

    ## [1] 15 14

``` r
corrplot(correlationMatrix)
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/remove%20redundant%20features-1.png)

``` r
transformed_pruned <- transformed_numeric %>% 
  select(-c(highlyCorrelated)) 
str(transformed_pruned) # 569 observations with 13 predictors
```

    ## 'data.frame':    395 obs. of  14 variables:
    ##  $ age       : num  1.022 0.238 -1.329 -1.329 -0.546 ...
    ##  $ Medu      : num  1.142 -1.598 -1.598 1.142 0.229 ...
    ##  $ Fedu      : num  1.359 -1.398 -1.398 -0.479 0.44 ...
    ##  $ traveltime: num  0.791 -0.642 -0.642 -0.642 -0.642 ...
    ##  $ studytime : num  -0.0422 -0.0422 -0.0422 1.1493 -0.0422 ...
    ##  $ failures  : num  -0.449 -0.449 3.585 -0.449 -0.449 ...
    ##  $ famrel    : num  0.0621 1.1774 0.0621 -1.0531 0.0621 ...
    ##  $ freetime  : num  -0.236 -0.236 -0.236 -1.237 -0.236 ...
    ##  $ goout     : num  0.8005 -0.0978 -0.996 -0.996 -0.996 ...
    ##  $ Dalc      : num  -0.54 -0.54 0.583 -0.54 -0.54 ...
    ##  $ Walc      : num  -1.003 -1.003 0.55 -1.003 -0.226 ...
    ##  $ health    : num  -0.399 -0.399 -0.399 1.04 1.04 ...
    ##  $ absences  : num  0.0364 -0.2135 0.5362 -0.4634 -0.2135 ...
    ##  $ G3        : num  -0.9637 -0.9637 -0.0906 1.0007 -0.0906 ...

``` r
data <- cbind(transformed_pruned$G3, transformed_pruned[, 1:13], transformed_factors) # do not want G1.y and G2.y (1st and 2nd period grades) since these are highly correlated with the final grade, but make the modeling less useful
 # We just want to predict final grades based off of student attributes and not previous grades
names(data)[1] <- "FinalGrade"

str(data)
```

    ## 'data.frame':    395 obs. of  31 variables:
    ##  $ FinalGrade: num  -0.9637 -0.9637 -0.0906 1.0007 -0.0906 ...
    ##  $ age       : num  1.022 0.238 -1.329 -1.329 -0.546 ...
    ##  $ Medu      : num  1.142 -1.598 -1.598 1.142 0.229 ...
    ##  $ Fedu      : num  1.359 -1.398 -1.398 -0.479 0.44 ...
    ##  $ traveltime: num  0.791 -0.642 -0.642 -0.642 -0.642 ...
    ##  $ studytime : num  -0.0422 -0.0422 -0.0422 1.1493 -0.0422 ...
    ##  $ failures  : num  -0.449 -0.449 3.585 -0.449 -0.449 ...
    ##  $ famrel    : num  0.0621 1.1774 0.0621 -1.0531 0.0621 ...
    ##  $ freetime  : num  -0.236 -0.236 -0.236 -1.237 -0.236 ...
    ##  $ goout     : num  0.8005 -0.0978 -0.996 -0.996 -0.996 ...
    ##  $ Dalc      : num  -0.54 -0.54 0.583 -0.54 -0.54 ...
    ##  $ Walc      : num  -1.003 -1.003 0.55 -1.003 -0.226 ...
    ##  $ health    : num  -0.399 -0.399 -0.399 1.04 1.04 ...
    ##  $ absences  : num  0.0364 -0.2135 0.5362 -0.4634 -0.2135 ...
    ##  $ school    : Factor w/ 2 levels "GP","MS": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ sex       : Factor w/ 2 levels "F","M": 1 1 1 1 1 2 2 1 2 2 ...
    ##  $ address   : Factor w/ 2 levels "R","U": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ famsize   : Factor w/ 2 levels "GT3","LE3": 1 1 2 1 1 2 2 1 2 1 ...
    ##  $ Pstatus   : Factor w/ 2 levels "A","T": 1 2 2 2 2 2 2 1 1 2 ...
    ##  $ Mjob      : Factor w/ 5 levels "at_home","health",..: 1 1 1 2 3 4 3 3 4 3 ...
    ##  $ Fjob      : Factor w/ 5 levels "at_home","health",..: 5 3 3 4 3 3 3 5 3 3 ...
    ##  $ reason    : Factor w/ 4 levels "course","home",..: 1 1 3 2 2 4 2 2 2 2 ...
    ##  $ guardian  : Factor w/ 3 levels "father","mother",..: 2 1 2 2 1 2 2 2 2 2 ...
    ##  $ schoolsup : Factor w/ 2 levels "no","yes": 2 1 2 1 1 1 1 2 1 1 ...
    ##  $ famsup    : Factor w/ 2 levels "no","yes": 1 2 1 2 2 2 1 2 2 2 ...
    ##  $ paid      : Factor w/ 2 levels "no","yes": 1 1 2 2 2 2 1 1 2 2 ...
    ##  $ activities: Factor w/ 2 levels "no","yes": 1 1 1 2 1 2 1 1 1 2 ...
    ##  $ nursery   : Factor w/ 2 levels "no","yes": 2 1 2 2 2 2 2 2 2 2 ...
    ##  $ higher    : Factor w/ 2 levels "no","yes": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ internet  : Factor w/ 2 levels "no","yes": 1 2 2 2 1 2 2 1 2 2 ...
    ##  $ romantic  : Factor w/ 2 levels "no","yes": 1 1 1 2 1 1 1 1 1 1 ...

Rank features by importance
---------------------------

Based off of importance ranking, the student's number of failures, their mother's and father's education levels, student age, and whether the student wants to pursue higher education are all useful predictors of the student's final grade. Whereas the the student's amount of freetime after school, whether they are involved in extra-curricular activities and their number of school absences all appear to be less helpful predictors.

``` r
seed <- 23
set.seed(seed)
control <- trainControl(method = "repeatedcv", number = 10, repeats = 3) # prepare training scheme
model <- train(FinalGrade ~ ., data = data, method = "BstLm", preProcess = "scale", trControl = control) # train the model
importance <- varImp(model, scale = FALSE) # estimate variable importance
print(importance) # summarize importance
```

    ## loess r-squared variable importance
    ## 
    ##   only 20 most important variables shown (out of 30)
    ## 
    ##             Overall
    ## failures   0.129899
    ## Medu       0.047153
    ## higher     0.033293
    ## age        0.026108
    ## Fedu       0.023243
    ## goout      0.017634
    ## romantic   0.016892
    ## reason     0.014883
    ## traveltime 0.013722
    ## address    0.011184
    ## sex        0.010703
    ## Mjob       0.010421
    ## paid       0.010403
    ## internet   0.009699
    ## studytime  0.009569
    ## schoolsup  0.006854
    ## famsize    0.006627
    ## guardian   0.004915
    ## health     0.003762
    ## Pstatus    0.003365

``` r
plot(importance) # plot importance
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/rank%20features-1.png)

Feature selection
-----------------

Feature selection shows that all 30 predictors are needed to minimize the cross-validation root mean squared error.

``` r
set.seed(seed)
control <- rfeControl(functions = rfFuncs, method = "cv", number = 10) # define the control using a random forest selection function
results <- rfe(data[ , -1], data$FinalGrade, sizes = c(1:17), rfeControl = control) # run the RFE algorithm
print(results) # summarize the results
```

    ## 
    ## Recursive feature selection
    ## 
    ## Outer resampling method: Cross-Validated (10 fold) 
    ## 
    ## Resampling performance over subset size:
    ## 
    ##  Variables   RMSE Rsquared  RMSESD RsquaredSD Selected
    ##          1 0.9299   0.1377 0.12239     0.0858         
    ##          2 0.8755   0.2384 0.09918     0.1799         
    ##          3 0.8753   0.2437 0.10485     0.1813         
    ##          4 0.8881   0.2371 0.11400     0.1854         
    ##          5 0.8914   0.2332 0.11963     0.1762         
    ##          6 0.8656   0.2444 0.10406     0.1641         
    ##          7 0.8774   0.2292 0.13024     0.1630         
    ##          8 0.8754   0.2379 0.14797     0.1663         
    ##          9 0.8717   0.2442 0.14666     0.1574         
    ##         10 0.8588   0.2594 0.14905     0.1611         
    ##         11 0.8575   0.2567 0.14879     0.1595         
    ##         12 0.8649   0.2440 0.14495     0.1329         
    ##         13 0.8544   0.2607 0.13695     0.1620         
    ##         14 0.8606   0.2535 0.13763     0.1652         
    ##         15 0.8531   0.2713 0.13927     0.1893         
    ##         16 0.8580   0.2643 0.14158     0.1865         
    ##         17 0.8572   0.2655 0.13846     0.1799         
    ##         30 0.8373   0.3032 0.13619     0.1795        *
    ## 
    ## The top 5 variables (out of 30):
    ##    failures, absences, schoolsup, higher, goout

``` r
predictors(results) # list the chosen features
```

    ##  [1] "failures"   "absences"   "schoolsup"  "higher"     "goout"     
    ##  [6] "Medu"       "sex"        "guardian"   "Mjob"       "age"       
    ## [11] "school"     "Dalc"       "Pstatus"    "activities" "reason"    
    ## [16] "paid"       "Walc"       "famsup"     "freetime"   "famrel"    
    ## [21] "health"     "Fedu"       "romantic"   "nursery"    "address"   
    ## [26] "traveltime" "studytime"  "Fjob"       "famsize"    "internet"

``` r
selected_predictors <- predictors(results)
plot(results, type=c("g", "o")) # plot the results
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/feature%20selection-1.png)

``` r
#data <- data %>% 
#  select(FinalGrade, selected_predictors)
#str(data)
```

Spot-checking ML algorithms
---------------------------

Models that appear useful for accurate prediction include gbm, glmnet, glm, svmRadial, rf and C5.0.

{r spot checking algorithms, message=FALSE, warning=FALSE} control &lt;- trainControl(method = "repeatedcv", number = 10, repeats=3) set.seed(seed) metric &lt;- "Accuracy" \# define test metric to compare models

algorithms &lt;- c("lda", "glm", "glmnet", "svmRadial", "knn", "nb", "rpart", "C5.0", "treebag", "rf", "gbm")

garbage &lt;- capture.output(models &lt;- caretList(diagnosis ~ ., data = data, trControl = control, methodList = algorithms)) results &lt;- resamples(models) summary(results) dotplot(results) cormodels &lt;- modelCor(results) print(cormodels) corrplot(cormodels)

Tune best ML algorithms
-----------------------

With model tuning, we can slightly increase the prediction accuracies of the best machine learning algorithms. After tuning, the most accurate model is a gradient boosting machine (gbm) with 1,000 trees, a shrinkage parameter of 0.01, and a minimum of 15 observations in the terminal tree nodes, and the model allows for 2-way interactions between predictor variables. This model has an accuracy of 0.9696.

{r tuning best algorithms, message=FALSE, warning=FALSE} \# glm modelLookup(model = "glm") \# look up the tuning parameter of the model set.seed(seed) glm\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "glm", metric = metric, trControl = control) print(glm\_gridsearch)

gbm
===

tunegrid &lt;- expand.grid(n.trees = c(900, 1000, 1100), interaction.depth = c(1, 2), shrinkage = c(0.01), n.minobsinnode = c(15, 20)) set.seed(seed) garbage &lt;- capture.output(gbm\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "gbm", metric = metric, tuneGrid = tunegrid, trControl = control)) print(gbm\_gridsearch) max(gbm\_gridsearch*r**e**s**u**l**t**s*Accuracy)

glmnet
======

tunegrid &lt;- expand.grid(alpha = c(0.5, 0.75, 1), lambda = c(0.0001, 0.0002, 0.0003)) set.seed(seed) glmnet\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "glmnet", metric = metric, tuneGrid = tunegrid, trControl = control) print(glmnet\_gridsearch) max(glmnet\_gridsearch*r**e**s**u**l**t**s*Accuracy)

svmRadial
=========

tunegrid &lt;- expand.grid(sigma = c(0.14, 0.15, 0.16, 0.17, 0.18), C = c(0.75, 0.80, 0.90, 1)) set.seed(seed) svm\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "svmRadial", metric = metric, tuneGrid = tunegrid, trControl = control) svm\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "svmRadial", metric = metric, tunelength = 5, trControl = control) print(svm\_gridsearch) max(svm\_gridsearch*r**e**s**u**l**t**s*Accuracy)

rf
==

mtry &lt;- sqrt(ncol(data\[ , 2:9\])) \# default for rf tuning parameter is 2.828427 tunegrid &lt;- expand.grid(.mtry = c(1:8)) set.seed(seed) rf\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "rf", metric = metric, tuneGrid = tunegrid, trControl = control) print(rf\_gridsearch) max(rf\_gridsearch*r**e**s**u**l**t**s*Accuracy)

C5.0
====

tunegrid &lt;- expand.grid(.trials = c(5:15), .model = "tree", .winnow = FALSE) set.seed(seed) C5.0\_gridsearch &lt;- train(diagnosis ~ ., data = data, method = "C5.0", metric = metric, tuneGrid = tunegrid, trControl = control) print(C5.0\_gridsearch) max(C5.0\_gridsearch*r**e**s**u**l**t**s*Accuracy)

Ensemble modeling
-----------------

Stacking the best machine learning models together with a random forest algorithm, slightly improves upon the accuracy of the best tuned gradient boosting machine (which had an accuracy of 0.9696). The accuracy of the stacked model is 0.977, and the model includes the generalized linear model (glm), radial support vector machine (svmRadial), the random forest model, C5.0, and the gradient boosting machine. I did not include the glmnet model since this was highly correlated (&gt; 0.75) with the generalized linear model.

{r, message=FALSE, warning=FALSE} algorithmsList2 = c("glm", "svmRadial", "C5.0", "rf", "gbm")

stackControl &lt;- trainControl(method="repeatedcv", number=10, repeats=3, savePredictions=TRUE, classProbs=TRUE) set.seed(seed) garbage &lt;- capture.output(models2 &lt;- caretList(diagnosis ~ ., data = data, trControl = stackControl, methodList = algorithmsList2))

set.seed(seed) stack.glm &lt;- caretStack(models2, method = "glm", metric = "Accuracy", trControl=stackControl) \# stack models with glm \# try out different kinds of models to stack to see which one is most accurate print(stack.glm) \# 0.9726613

set.seed(seed) stack.rf &lt;- caretStack(models2, method = "rf", metric = "Accuracy", trControl=stackControl) \# stack models with glm \# try out different kinds of models to stack to see which one is most accurate print(stack.rf) \# 0.9769578

To conclude, a stacked machine learning model with eight breast tumor characteristics is quite accurate (0.977) at determining whether the tumor is benign or malignant. This statistical approach could be applied to help improve breast cancer diagnoses and detection.
