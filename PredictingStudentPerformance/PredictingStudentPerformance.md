Predicting student performance
================

Objective
---------

Accurately predict a student's performance (final grade) in math from various attributes (e.g., family size, age, weekly study time, health, etc.) about the student.

Data
----

The dataset includes the student's final math score (performance) along with 30 different characteristics about the student (e.g., family information, health, time spent studying, etc.). The data are freely available on the [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/datasets/Student+Performance).

``` r
studentdata <- read.csv("~/Documents/Data Science/GitHub_repository/MachineLearningPortfolio/PredictingStudentPerformance/student_data/student-mat.csv", sep = ";", header = TRUE) # student math scores and characteristics

str(studentdata) # 395 students with 33 variables, "G3.y" is the final grade and predictor target
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
studentdata %>% 
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
studentdata %>% 
  boxplot(use.cols = TRUE) # a few fairly skewed predictors and variable scales
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/load%20+%20explore%20data-1.png)

Data transformation
-------------------

After trying out various transformations (including centering, scaling, normalization, BoxCox, and YeoJohnson), I have decided to use standardization which helps to make the data more normal in distribution while still maintaining variation in the predictors.

``` r
preprocessParams <- preProcess(studentdata, method = c("center", "scale")) # calculate the pre-process parameters from the dataset by standardizing them
print(preprocessParams) # summarize transform parameters
```

    ## Created from 395 samples and 33 variables
    ## 
    ## Pre-processing:
    ##   - centered (16)
    ##   - ignored (17)
    ##   - scaled (16)

``` r
transformed <- predict(preprocessParams, studentdata) # transform the dataset using the parameters
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
studentdata <- cbind(transformed_pruned$G3, transformed_pruned[, 1:13], transformed_factors) # do not want G1.y and G2.y (1st and 2nd period grades) since these are highly correlated with the final grade, but make the modeling less useful
 # We just want to predict final grades based off of student attributes and not previous grades
names(studentdata)[1] <- "FinalGrade"

str(studentdata)
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
model <- train(FinalGrade ~ ., data = studentdata, method = "BstLm", preProcess = "scale", trControl = control) # train the model
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
results <- rfe(studentdata[ , -1], studentdata$FinalGrade, sizes = c(1:17), rfeControl = control) # run the RFE algorithm
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

Spot-checking ML algorithms
---------------------------

Models that appear useful for accurate prediction (minimized root mean squared error) include gbm, rf, treebag, and rpart. In addition these useful models are all fairly correlated with eachother and thus model stacking may not improve prediction accuracy.

``` r
control <- trainControl(method = "repeatedcv", number = 10, repeats=3)
set.seed(seed)
metric <- "RMSE" # define test metric to compare models

algorithms <- c("glmboost", "gam", "glmnet", "svmRadial", "mlp", "knn", "rpart", "treebag", "rf", "gbm", "lasso") # regression algorithms

garbage <- capture.output(models <- caretList(FinalGrade ~ ., data = studentdata, trControl = control, methodList = algorithms))
results <- resamples(models)
summary(results)
```

    ## 
    ## Call:
    ## summary.resamples(object = results)
    ## 
    ## Models: glmboost, gam, glmnet, svmRadial, mlp, knn, rpart, treebag, rf, gbm, lasso 
    ## Number of resamples: 30 
    ## 
    ## RMSE 
    ##             Min. 1st Qu. Median   Mean 3rd Qu.   Max. NA's
    ## glmboost  0.6443  0.8850 0.9282 0.9286  0.9817 1.1610    0
    ## gam       0.7474  0.8614 0.9255 0.9264  0.9816 1.1390    0
    ## glmnet    0.6422  0.8794 0.9389 0.9305  0.9904 1.1730    0
    ## svmRadial 0.5967  0.8469 0.9400 0.9196  0.9868 1.1990    0
    ## mlp       0.6635  0.8832 0.9904 0.9795  1.0650 1.2620    0
    ## knn       0.6192  0.8669 0.9826 0.9468  1.0060 1.1890    0
    ## rpart     0.6968  0.8452 0.9238 0.9113  0.9709 1.0780    0
    ## treebag   0.6411  0.7811 0.8456 0.8526  0.9119 1.0570    0
    ## rf        0.5886  0.7848 0.8132 0.8405  0.8947 1.0460    0
    ## gbm       0.6224  0.8014 0.8468 0.8506  0.9195 0.9895    0
    ## lasso     0.6562  0.8822 0.9220 0.9282  0.9741 1.1560    0
    ## 
    ## Rsquared 
    ##                Min. 1st Qu.  Median   Mean 3rd Qu.   Max. NA's
    ## glmboost  1.901e-02 0.06849 0.11340 0.1475  0.2201 0.4519    0
    ## gam       2.859e-03 0.10590 0.16510 0.1864  0.2438 0.5217    0
    ## glmnet    1.693e-02 0.06604 0.11260 0.1445  0.2208 0.4389    0
    ## svmRadial 7.962e-03 0.11000 0.15920 0.1643  0.2073 0.3566    0
    ## mlp       3.978e-05 0.04354 0.08777 0.1380  0.1798 0.6061    0
    ## knn       5.692e-04 0.05310 0.11640 0.1165  0.1478 0.3694    0
    ## rpart     5.004e-07 0.04588 0.20170 0.2008  0.3251 0.5624    0
    ## treebag   2.751e-02 0.14480 0.21680 0.2699  0.4178 0.6064    0
    ## rf        5.331e-02 0.13300 0.27070 0.2975  0.4222 0.6555    0
    ## gbm       5.280e-02 0.12190 0.24480 0.2701  0.4125 0.6018    0
    ## lasso     1.295e-02 0.07452 0.12370 0.1497  0.2162 0.4752    0

``` r
dotplot(results)
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/spot%20checking%20algorithms-1.png)

``` r
cormodels <- modelCor(results)
print(cormodels)
```

    ##            glmboost       gam    glmnet svmRadial       mlp       knn
    ## glmboost  1.0000000 0.7742958 0.9986327 0.9428175 0.7752308 0.9045990
    ## gam       0.7742958 1.0000000 0.7560791 0.6975535 0.6617136 0.6001555
    ## glmnet    0.9986327 0.7560791 1.0000000 0.9476446 0.7632615 0.9097512
    ## svmRadial 0.9428175 0.6975535 0.9476446 1.0000000 0.7432219 0.9416852
    ## mlp       0.7752308 0.6617136 0.7632615 0.7432219 1.0000000 0.7721056
    ## knn       0.9045990 0.6001555 0.9097512 0.9416852 0.7721056 1.0000000
    ## rpart     0.4273335 0.3677891 0.4141704 0.3399188 0.3821655 0.3838986
    ## treebag   0.6989512 0.5746011 0.6847208 0.6342849 0.6362725 0.6900036
    ## rf        0.8117863 0.6978115 0.8019424 0.7739424 0.7108624 0.7914000
    ## gbm       0.7776508 0.7164573 0.7626981 0.7153208 0.7300175 0.7363712
    ## lasso     0.9969394 0.8011239 0.9928828 0.9388553 0.7800779 0.8914106
    ##               rpart   treebag        rf       gbm     lasso
    ## glmboost  0.4273335 0.6989512 0.8117863 0.7776508 0.9969394
    ## gam       0.3677891 0.5746011 0.6978115 0.7164573 0.8011239
    ## glmnet    0.4141704 0.6847208 0.8019424 0.7626981 0.9928828
    ## svmRadial 0.3399188 0.6342849 0.7739424 0.7153208 0.9388553
    ## mlp       0.3821655 0.6362725 0.7108624 0.7300175 0.7800779
    ## knn       0.3838986 0.6900036 0.7914000 0.7363712 0.8914106
    ## rpart     1.0000000 0.7939789 0.7360467 0.7286550 0.4146105
    ## treebag   0.7939789 1.0000000 0.9488914 0.8963963 0.6896669
    ## rf        0.7360467 0.9488914 1.0000000 0.9413480 0.8039603
    ## gbm       0.7286550 0.8963963 0.9413480 1.0000000 0.7788929
    ## lasso     0.4146105 0.6896669 0.8039603 0.7788929 1.0000000

``` r
corrplot(cormodels)
```

![](PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/spot%20checking%20algorithms-2.png)

Tune best ML algorithms
-----------------------

With model tuning, we can slightly increase the prediction accuracies of the best machine learning algorithms. After tuning, the most accurate model is a random forest where 14 randomly selected predictor variables are used at each split within the decision tree. This model has a root mean squared error (RMSE) of 0.843.

``` r
# gbm
modelLookup(model = "gbm")
```

    ##   model         parameter                   label forReg forClass
    ## 1   gbm           n.trees   # Boosting Iterations   TRUE     TRUE
    ## 2   gbm interaction.depth          Max Tree Depth   TRUE     TRUE
    ## 3   gbm         shrinkage               Shrinkage   TRUE     TRUE
    ## 4   gbm    n.minobsinnode Min. Terminal Node Size   TRUE     TRUE
    ##   probModel
    ## 1      TRUE
    ## 2      TRUE
    ## 3      TRUE
    ## 4      TRUE

``` r
tunegrid <- expand.grid(n.trees = c(800, 900), interaction.depth = c(1, 2), 
                        shrinkage = c(0.01), n.minobsinnode = c(3, 5)) 
set.seed(seed)

garbage <- capture.output(gbm_gridsearch <- caret::train(FinalGrade ~ ., data = studentdata, method = "gbm", 
                                                  metric = metric, tuneGrid = tunegrid, trControl = control))
print(gbm_gridsearch)
```

    ## Stochastic Gradient Boosting 
    ## 
    ## 395 samples
    ##  30 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 355, 356, 357, 355, 355, 354, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   interaction.depth  n.minobsinnode  n.trees  RMSE       Rsquared 
    ##   1                  3               800      0.8853811  0.2190930
    ##   1                  3               900      0.8834045  0.2219351
    ##   1                  5               800      0.8864584  0.2173022
    ##   1                  5               900      0.8844112  0.2207434
    ##   2                  3               800      0.8484817  0.2764960
    ##   2                  3               900      0.8485218  0.2767355
    ##   2                  5               800      0.8483467  0.2761407
    ##   2                  5               900      0.8479729  0.2773923
    ## 
    ## Tuning parameter 'shrinkage' was held constant at a value of 0.01
    ## RMSE was used to select the optimal model using  the smallest value.
    ## The final values used for the model were n.trees = 900,
    ##  interaction.depth = 2, shrinkage = 0.01 and n.minobsinnode = 5.

``` r
min(gbm_gridsearch$results$RMSE) # 0.8478651
```

    ## [1] 0.8479729

``` r
# treebag
modelLookup(model = "treebag")
```

    ##     model parameter     label forReg forClass probModel
    ## 1 treebag parameter parameter   TRUE     TRUE      TRUE

``` r
set.seed(seed)
treebag_mod <- caret::train(FinalGrade ~ ., data = studentdata, method = "treebag", metric = metric,
                            trControl = control)
treebag_gridsearch <- caret::train(FinalGrade ~ ., data = studentdata, method = "treebag", metric = metric, tunelength = 5,
                       trControl = control)
print(treebag_mod) # 0.8593949
```

    ## Bagged CART 
    ## 
    ## 395 samples
    ##  30 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 355, 356, 357, 355, 355, 354, ... 
    ## Resampling results:
    ## 
    ##   RMSE      Rsquared 
    ##   0.847604  0.2810426

``` r
print(treebag_gridsearch) # 0.8442427
```

    ## Bagged CART 
    ## 
    ## 395 samples
    ##  30 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 356, 356, 356, 356, 354, 354, ... 
    ## Resampling results:
    ## 
    ##   RMSE       Rsquared 
    ##   0.8465247  0.2999178

``` r
# rf
modelLookup(model = "rf")
```

    ##   model parameter                         label forReg forClass probModel
    ## 1    rf      mtry #Randomly Selected Predictors   TRUE     TRUE      TRUE

``` r
mtry <- sqrt(ncol(studentdata[ , -1])) # default for rf tuning parameter is 5.477226
tunegrid <- expand.grid(.mtry = c(1:15))
set.seed(seed)
rf_gridsearch <- caret::train(FinalGrade ~ ., data = studentdata, method = "rf", metric = metric, tuneGrid = tunegrid,
                       trControl = control)
print(rf_gridsearch)
```

    ## Random Forest 
    ## 
    ## 395 samples
    ##  30 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 355, 356, 357, 355, 355, 354, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   mtry  RMSE       Rsquared 
    ##    1    0.9406732  0.1882839
    ##    2    0.9084331  0.2175828
    ##    3    0.8925893  0.2381934
    ##    4    0.8850598  0.2444083
    ##    5    0.8750945  0.2573038
    ##    6    0.8667886  0.2706480
    ##    7    0.8636332  0.2727488
    ##    8    0.8577813  0.2821451
    ##    9    0.8554245  0.2816697
    ##   10    0.8493789  0.2928952
    ##   11    0.8465483  0.2962357
    ##   12    0.8464486  0.2926188
    ##   13    0.8437584  0.2981257
    ##   14    0.8434868  0.2961594
    ##   15    0.8445827  0.2921483
    ## 
    ## RMSE was used to select the optimal model using  the smallest value.
    ## The final value used for the model was mtry = 14.

``` r
min(rf_gridsearch$results$RMSE) # 0.8434868
```

    ## [1] 0.8434868

``` r
# rpart
modelLookup(model = "rpart")
```

    ##   model parameter                label forReg forClass probModel
    ## 1 rpart        cp Complexity Parameter   TRUE     TRUE      TRUE

``` r
tunegrid <- expand.grid(.cp = c(0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.07, 0.08, 0.09, 0.1, 0.15)) 
set.seed(seed)
rpart_gridsearch <- caret::train(FinalGrade ~ ., data = studentdata, method = "rpart", metric = metric, tuneGrid = tunegrid,
                       trControl = control)

print(rpart_gridsearch)
```

    ## CART 
    ## 
    ## 395 samples
    ##  30 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 355, 356, 357, 355, 355, 354, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   cp    RMSE       Rsquared   
    ##   0.01  0.9417633  0.193104579
    ##   0.02  0.9022917  0.218694622
    ##   0.03  0.8828974  0.222740543
    ##   0.04  0.8702648  0.235908536
    ##   0.05  0.8702648  0.235908536
    ##   0.06  0.8702648  0.235908536
    ##   0.07  0.8702648  0.235908536
    ##   0.08  0.8702648  0.235908536
    ##   0.09  0.8702648  0.235908536
    ##   0.10  0.8983565  0.201510370
    ##   0.15  0.9939951  0.006150281
    ## 
    ## RMSE was used to select the optimal model using  the smallest value.
    ## The final value used for the model was cp = 0.09.

``` r
min(rpart_gridsearch$results$RMSE) # 0.8702648
```

    ## [1] 0.8702648

Model stacking
--------------

I've decided to try model stacking with just the best tuned model, random forest, and a recursive partitioning regression tree (rpart) since rpart was still fairly good at prediction (RMSE = 0.87) and was not too strongly correlated with the random forest model (0.736 correlation). After testing out a few different ways of stacking these models, I discovered that using a gradient boosting machine to stack the rpart and rf models results in a much improved model (RMSE = 0.81).

``` r
algorithmsList2 <- c("rf", "rpart")

stackControl <- trainControl(method="repeatedcv", number=10, repeats=3, savePredictions=TRUE, classProbs=TRUE)
set.seed(seed)
garbage <- capture.output(models2 <- caretList(FinalGrade ~ ., data = studentdata, trControl = stackControl, methodList = algorithmsList2))


set.seed(seed)
stack.glm <- caretStack(models2, method = "glm", metric = "RMSE", trControl=stackControl) # stack models with glm
print(stack.glm) # 0.8441826
```

    ## A glm ensemble of 2 base models: rf, rpart
    ## 
    ## Ensemble results:
    ## Generalized Linear Model 
    ## 
    ## 1185 samples
    ##    2 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 1067, 1066, 1066, 1066, 1067, 1065, ... 
    ## Resampling results:
    ## 
    ##   RMSE       Rsquared 
    ##   0.8441826  0.2906907

``` r
garbage <- capture.output(stack.gbm <- caretStack(models2, method = "gbm", metric = "RMSE", trControl=stackControl)) # stack models with gbm
print(stack.gbm) # 0.8101679, 0.34 R squared
```

    ## A gbm ensemble of 2 base models: rf, rpart
    ## 
    ## Ensemble results:
    ## Stochastic Gradient Boosting 
    ## 
    ## 1185 samples
    ##    2 predictor
    ## 
    ## No pre-processing
    ## Resampling: Cross-Validated (10 fold, repeated 3 times) 
    ## Summary of sample sizes: 1067, 1067, 1066, 1067, 1066, 1067, ... 
    ## Resampling results across tuning parameters:
    ## 
    ##   interaction.depth  n.trees  RMSE       Rsquared 
    ##   1                   50      0.8164694  0.3371441
    ##   1                  100      0.8104543  0.3427557
    ##   1                  150      0.8111513  0.3417136
    ##   2                   50      0.8101679  0.3433616
    ##   2                  100      0.8124560  0.3395967
    ##   2                  150      0.8168911  0.3335865
    ##   3                   50      0.8116170  0.3408505
    ##   3                  100      0.8172995  0.3326164
    ##   3                  150      0.8223481  0.3263624
    ## 
    ## Tuning parameter 'shrinkage' was held constant at a value of 0.1
    ## 
    ## Tuning parameter 'n.minobsinnode' was held constant at a value of 10
    ## RMSE was used to select the optimal model using  the smallest value.
    ## The final values used for the model were n.trees = 50, interaction.depth
    ##  = 2, shrinkage = 0.1 and n.minobsinnode = 10.

To conclude, a stacked model (via a gradient boosting machine) that includes both a random forest and recursive partitioning regression tree models with all 30 predictor variables is the best model at predicting a student's final test score (performance). While this stacked model surpasses other algorithm types (e.g., generalized linear models, support vector machines) and published studies using the same dataset (Cortez and Silva 2008, best RMSE was 1.75), much of the variation in student performance is still not explained (R squared is 0.34). Thus, likely other factors not explored in this dataset contribute to student performance (e.g., instructor information, socioeconomic variables, class size). Also, as a next step, it would be interesting to test whether student learning gains (i.e., the difference between their final score and their initial score, "G1") could be more accurately predicted. Perhaps final score in itself has a lot of variation that can't be explained or predicted well given our set of predictor variables, but *changes* in learning outcomes could be more readily modeled.
