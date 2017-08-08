Predicting breast cancer from mass characteristics
================

Objective
---------

Accurately predict whether a breast mass is benign or malignant based off of various characteristics (e.g., symmetry, area, texture, etc.) of the mass.

Data
----

The dataset includes the patient diagnosis (212 patients with malignant masses and 357 patients with benign masses) with 30 different mass characteristics. The data were originally from the Univeristy of Wisconsin - Madison and are now freely available on [Kaggle](https://www.kaggle.com/uciml/breast-cancer-wisconsin-data) and the [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Breast+Cancer+Wisconsin+%28Diagnostic%29).

``` r
data <- read.csv("~/Documents/Data Science/GitHub_repository/MachineLearningPortfolio/PredictingBreastCancer/BreastCancerData.csv")
str(data) # 569 observations with 32 variables
```

    ## 'data.frame':    569 obs. of  33 variables:
    ##  $ id                     : int  842302 842517 84300903 84348301 84358402 843786 844359 84458202 844981 84501001 ...
    ##  $ diagnosis              : Factor w/ 2 levels "B","M": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ radius_mean            : num  18 20.6 19.7 11.4 20.3 ...
    ##  $ texture_mean           : num  10.4 17.8 21.2 20.4 14.3 ...
    ##  $ perimeter_mean         : num  122.8 132.9 130 77.6 135.1 ...
    ##  $ area_mean              : num  1001 1326 1203 386 1297 ...
    ##  $ smoothness_mean        : num  0.1184 0.0847 0.1096 0.1425 0.1003 ...
    ##  $ compactness_mean       : num  0.2776 0.0786 0.1599 0.2839 0.1328 ...
    ##  $ concavity_mean         : num  0.3001 0.0869 0.1974 0.2414 0.198 ...
    ##  $ concave.points_mean    : num  0.1471 0.0702 0.1279 0.1052 0.1043 ...
    ##  $ symmetry_mean          : num  0.242 0.181 0.207 0.26 0.181 ...
    ##  $ fractal_dimension_mean : num  0.0787 0.0567 0.06 0.0974 0.0588 ...
    ##  $ radius_se              : num  1.095 0.543 0.746 0.496 0.757 ...
    ##  $ texture_se             : num  0.905 0.734 0.787 1.156 0.781 ...
    ##  $ perimeter_se           : num  8.59 3.4 4.58 3.44 5.44 ...
    ##  $ area_se                : num  153.4 74.1 94 27.2 94.4 ...
    ##  $ smoothness_se          : num  0.0064 0.00522 0.00615 0.00911 0.01149 ...
    ##  $ compactness_se         : num  0.049 0.0131 0.0401 0.0746 0.0246 ...
    ##  $ concavity_se           : num  0.0537 0.0186 0.0383 0.0566 0.0569 ...
    ##  $ concave.points_se      : num  0.0159 0.0134 0.0206 0.0187 0.0188 ...
    ##  $ symmetry_se            : num  0.03 0.0139 0.0225 0.0596 0.0176 ...
    ##  $ fractal_dimension_se   : num  0.00619 0.00353 0.00457 0.00921 0.00511 ...
    ##  $ radius_worst           : num  25.4 25 23.6 14.9 22.5 ...
    ##  $ texture_worst          : num  17.3 23.4 25.5 26.5 16.7 ...
    ##  $ perimeter_worst        : num  184.6 158.8 152.5 98.9 152.2 ...
    ##  $ area_worst             : num  2019 1956 1709 568 1575 ...
    ##  $ smoothness_worst       : num  0.162 0.124 0.144 0.21 0.137 ...
    ##  $ compactness_worst      : num  0.666 0.187 0.424 0.866 0.205 ...
    ##  $ concavity_worst        : num  0.712 0.242 0.45 0.687 0.4 ...
    ##  $ concave.points_worst   : num  0.265 0.186 0.243 0.258 0.163 ...
    ##  $ symmetry_worst         : num  0.46 0.275 0.361 0.664 0.236 ...
    ##  $ fractal_dimension_worst: num  0.1189 0.089 0.0876 0.173 0.0768 ...
    ##  $ X                      : logi  NA NA NA NA NA NA ...

``` r
  # an extra column "X" was added to the end of the dataset that needs to be removed

data <- data %>% 
  select(-X)

data %>% 
  sapply(function(x) sum(is.na(x))) # no missing data
```

    ##                      id               diagnosis             radius_mean 
    ##                       0                       0                       0 
    ##            texture_mean          perimeter_mean               area_mean 
    ##                       0                       0                       0 
    ##         smoothness_mean        compactness_mean          concavity_mean 
    ##                       0                       0                       0 
    ##     concave.points_mean           symmetry_mean  fractal_dimension_mean 
    ##                       0                       0                       0 
    ##               radius_se              texture_se            perimeter_se 
    ##                       0                       0                       0 
    ##                 area_se           smoothness_se          compactness_se 
    ##                       0                       0                       0 
    ##            concavity_se       concave.points_se             symmetry_se 
    ##                       0                       0                       0 
    ##    fractal_dimension_se            radius_worst           texture_worst 
    ##                       0                       0                       0 
    ##         perimeter_worst              area_worst        smoothness_worst 
    ##                       0                       0                       0 
    ##       compactness_worst         concavity_worst    concave.points_worst 
    ##                       0                       0                       0 
    ##          symmetry_worst fractal_dimension_worst 
    ##                       0                       0

``` r
data %>% 
  select(-c(id, diagnosis)) %>% 
  boxplot(use.cols = TRUE) # fairly skewed predictors and variable scales
```

![](PredictingBreastCancer_files/figure-markdown_github-ascii_identifiers/load%20+%20explore%20data-1.png)

Data transformation
-------------------

After trying out various transformations (including centering, scaling, normalization, BoxCox, and YeoJohnson), I have decided to use standardization which helps to make the data more normal in distribution while maintaining outliers with large values that could be associated with malignant tumors.

``` r
preprocessParams <- preProcess(data[,3:32], method = c("center", "scale")) # calculate the pre-process parameters from the dataset by standardizing them
print(preprocessParams) # summarize transform parameters
```

    ## Created from 569 samples and 30 variables
    ## 
    ## Pre-processing:
    ##   - centered (30)
    ##   - ignored (0)
    ##   - scaled (30)

``` r
transformed <- predict(preprocessParams, data[,3:32]) # transform the dataset using the parameters
# summary(transformed) # summarize the transformed dataset
boxplot(transformed, use.cols = TRUE)
```

![](PredictingBreastCancer_files/figure-markdown_github-ascii_identifiers/data%20transformation-1.png)

Remove redundant features
-------------------------

``` r
correlationMatrix <- cor(transformed)
highlyCorrelated <- findCorrelation(correlationMatrix, cutoff = 0.75)
print(highlyCorrelated) # remove attributes with an absolute correlation of >= 0.75
```

    ##  [1]  7  8  6 28 27 23 21  3 26 24  1 13 18 16 14  5 10  2

``` r
transformed_pruned <- transformed %>% 
  select(-c(highlyCorrelated)) 
str(transformed_pruned) # 569 observations with 12 predictors
```

    ## 'data.frame':    569 obs. of  12 variables:
    ##  $ area_mean              : num  0.984 1.907 1.558 -0.764 1.825 ...
    ##  $ symmetry_mean          : num  2.21557 0.00139 0.93886 2.86486 -0.00955 ...
    ##  $ radius_se              : num  2.488 0.499 1.228 0.326 1.269 ...
    ##  $ texture_se             : num  -0.565 -0.875 -0.779 -0.11 -0.79 ...
    ##  $ smoothness_se          : num  -0.214 -0.605 -0.297 0.689 1.482 ...
    ##  $ concavity_se           : num  0.723 -0.44 0.213 0.819 0.828 ...
    ##  $ symmetry_se            : num  1.148 -0.805 0.237 4.729 -0.361 ...
    ##  $ fractal_dimension_se   : num  0.9063 -0.0994 0.2933 2.0457 0.4989 ...
    ##  $ texture_worst          : num  -1.358 -0.369 -0.024 0.134 -1.465 ...
    ##  $ smoothness_worst       : num  1.307 -0.375 0.527 3.391 0.22 ...
    ##  $ symmetry_worst         : num  2.748 -0.244 1.151 6.041 -0.868 ...
    ##  $ fractal_dimension_worst: num  1.935 0.281 0.201 4.931 -0.397 ...

``` r
data <- cbind(data$diagnosis, transformed_pruned)
names(data)[1] <- "diagnosis"
```

Rank features by importance
---------------------------

Based off of importance ranking, mean lump area, the standard error of the lump radius, lump texture, and the standard error of the lump concavity are all useful predictors of the tumor diagnosis. Whereas the standard errors of lump symmetry, smoothness, and texture all appear to be less helpful predictors.

``` r
seed <- 23
set.seed(seed)
control <- trainControl(method = "repeatedcv", number = 10, repeats = 3) # prepare training scheme
model <- train(diagnosis ~ ., data = data, method = "lvq", preProcess = "scale", trControl = control) # train the model
```

    ## Loading required package: class

``` r
importance <- varImp(model, scale = FALSE) # estimate variable importance
print(importance) # summarize importance
```

    ## ROC curve variable importance
    ## 
    ##                         Importance
    ## area_mean                   0.9383
    ## radius_se                   0.8683
    ## texture_worst               0.7846
    ## concavity_se                0.7808
    ## smoothness_worst            0.7541
    ## symmetry_worst              0.7369
    ## symmetry_mean               0.6986
    ## fractal_dimension_worst     0.6860
    ## fractal_dimension_se        0.6203
    ## symmetry_se                 0.5551
    ## smoothness_se               0.5312
    ## texture_se                  0.5116

``` r
plot(importance) # plot importance
```

![](PredictingBreastCancer_files/figure-markdown_github-ascii_identifiers/rank%20features-1.png)

Feature selection
-----------------

Feature selection shows that eight predictors give the best model accuracy, yet models with just five predictors are similar in accuracy. Thus, if a hospital was interested in streamlining their breast mass measurements, they could probably just assess these five characteristics (area\_mean, radius\_se, texture\_worst, smoothness\_worst, and symmytry\_worst) and still be able to accurately diagnose the tumor.

``` r
set.seed(seed)
control <- rfeControl(functions = rfFuncs, method = "cv", number = 10) # define the control using a random forest selection function
results <- rfe(data[,2:13], data$diagnosis, sizes = c(1:11), rfeControl = control) # run the RFE algorithm
print(results) # summarize the results
```

    ## 
    ## Recursive feature selection
    ## 
    ## Outer resampling method: Cross-Validated (10 fold) 
    ## 
    ## Resampling performance over subset size:
    ## 
    ##  Variables Accuracy  Kappa AccuracySD KappaSD Selected
    ##          1   0.8308 0.6372    0.06235 0.12568         
    ##          2   0.8979 0.7789    0.04349 0.09282         
    ##          3   0.9119 0.8088    0.04605 0.09906         
    ##          4   0.9489 0.8889    0.02954 0.06517         
    ##          5   0.9611 0.9160    0.03741 0.08177         
    ##          6   0.9630 0.9197    0.03183 0.07012         
    ##          7   0.9578 0.9082    0.03035 0.06693         
    ##          8   0.9631 0.9199    0.03048 0.06635        *
    ##          9   0.9630 0.9197    0.02966 0.06558         
    ##         10   0.9559 0.9041    0.04129 0.09132         
    ##         11   0.9613 0.9160    0.02983 0.06513         
    ##         12   0.9577 0.9076    0.03766 0.08388         
    ## 
    ## The top 5 variables (out of 8):
    ##    area_mean, radius_se, texture_worst, smoothness_worst, symmetry_worst

``` r
predictors(results) # list the chosen features
```

    ## [1] "area_mean"               "radius_se"              
    ## [3] "texture_worst"           "smoothness_worst"       
    ## [5] "symmetry_worst"          "concavity_se"           
    ## [7] "fractal_dimension_worst" "symmetry_se"

``` r
selected_predictors <- predictors(results)
plot(results, type=c("g", "o")) # plot the results
```

![](PredictingBreastCancer_files/figure-markdown_github-ascii_identifiers/feature%20selection-1.png)

``` r
data <- data %>% 
  select(diagnosis, selected_predictors)
```

Spot-checking ML algorithms
---------------------------

``` r
control <- trainControl(method = "repeatedcv", number = 10, repeats=3)
set.seed(seed)
metric <- "Accuracy" # define test metric to compare models

algorithms <- c("lda", "glm", "glmnet", "svmRadial", "knn", "nb", "rpart", "C5.0", "treebag", "rf", "gbm")

garbage <- capture.output(models <- caretList(diagnosis ~ ., data = data, trControl = control, methodList = algorithms))
results <- resamples(models)
summary(results)
```

    ## 
    ## Call:
    ## summary.resamples(object = results)
    ## 
    ## Models: lda, glm, glmnet, svmRadial, knn, nb, rpart, C5.0, treebag, rf, gbm 
    ## Number of resamples: 30 
    ## 
    ## Accuracy 
    ##             Min. 1st Qu. Median   Mean 3rd Qu.   Max. NA's
    ## lda       0.8772  0.9123 0.9469 0.9379  0.9648 0.9828    0
    ## glm       0.9123  0.9474 0.9655 0.9672  0.9825 1.0000    0
    ## glmnet    0.9107  0.9474 0.9652 0.9654  0.9825 1.0000    0
    ## svmRadial 0.9286  0.9467 0.9649 0.9631  0.9825 1.0000    0
    ## knn       0.8947  0.9298 0.9474 0.9479  0.9654 1.0000    0
    ## nb        0.8772  0.9123 0.9469 0.9378  0.9648 0.9828    0
    ## rpart     0.8246  0.8947 0.9286 0.9139  0.9474 0.9828    0
    ## C5.0      0.8571  0.9467 0.9646 0.9572  0.9824 1.0000    0
    ## treebag   0.8750  0.9301 0.9563 0.9478  0.9655 0.9828    0
    ## rf        0.8772  0.9474 0.9649 0.9619  0.9827 1.0000    0
    ## gbm       0.9107  0.9474 0.9652 0.9660  0.9827 1.0000    0
    ## 
    ## Kappa 
    ##             Min. 1st Qu. Median   Mean 3rd Qu.   Max. NA's
    ## lda       0.7223  0.8017 0.8829 0.8619  0.9229 0.9631    0
    ## glm       0.8057  0.8858 0.9274 0.9292  0.9627 1.0000    0
    ## glmnet    0.8039  0.8858 0.9260 0.9252  0.9627 1.0000    0
    ## svmRadial 0.8462  0.8840 0.9257 0.9208  0.9627 1.0000    0
    ## knn       0.7645  0.8445 0.8858 0.8859  0.9262 1.0000    0
    ## nb        0.7280  0.8096 0.8851 0.8651  0.9244 0.9631    0
    ## rpart     0.5992  0.7674 0.8454 0.8130  0.8834 0.9631    0
    ## C5.0      0.6832  0.8826 0.9246 0.9075  0.9626 1.0000    0
    ## treebag   0.7255  0.8480 0.9067 0.8867  0.9274 0.9631    0
    ## rf        0.7280  0.8840 0.9246 0.9172  0.9628 1.0000    0
    ## gbm       0.8039  0.8863 0.9253 0.9265  0.9630 1.0000    0

``` r
dotplot(results)
```

![](PredictingBreastCancer_files/figure-markdown_github-ascii_identifiers/spot%20checking%20algorithms-1.png)

``` r
modelCor(results)
```

    ##                 lda       glm    glmnet svmRadial       knn        nb
    ## lda       1.0000000 0.5650150 0.5373967 0.5271366 0.8137900 0.5527117
    ## glm       0.5650150 1.0000000 0.9638860 0.8278425 0.5610802 0.5568041
    ## glmnet    0.5373967 0.9638860 1.0000000 0.8077158 0.5153761 0.5027567
    ## svmRadial 0.5271366 0.8278425 0.8077158 1.0000000 0.5774048 0.5308291
    ## knn       0.8137900 0.5610802 0.5153761 0.5774048 1.0000000 0.5342363
    ## nb        0.5527117 0.5568041 0.5027567 0.5308291 0.5342363 1.0000000
    ## rpart     0.5438209 0.5301206 0.5419939 0.5528176 0.3551428 0.4403328
    ## C5.0      0.5659299 0.6431639 0.6613232 0.6840610 0.4855135 0.5473368
    ## treebag   0.6002255 0.5242944 0.5336560 0.6906743 0.4297164 0.3919987
    ## rf        0.5270456 0.6233759 0.6310086 0.6765944 0.4325898 0.4189381
    ## gbm       0.7106717 0.6338317 0.6625830 0.6404505 0.6040944 0.4065226
    ##               rpart      C5.0   treebag        rf       gbm
    ## lda       0.5438209 0.5659299 0.6002255 0.5270456 0.7106717
    ## glm       0.5301206 0.6431639 0.5242944 0.6233759 0.6338317
    ## glmnet    0.5419939 0.6613232 0.5336560 0.6310086 0.6625830
    ## svmRadial 0.5528176 0.6840610 0.6906743 0.6765944 0.6404505
    ## knn       0.3551428 0.4855135 0.4297164 0.4325898 0.6040944
    ## nb        0.4403328 0.5473368 0.3919987 0.4189381 0.4065226
    ## rpart     1.0000000 0.7140943 0.7035997 0.6575943 0.4584819
    ## C5.0      0.7140943 1.0000000 0.8073312 0.7871779 0.6679394
    ## treebag   0.7035997 0.8073312 1.0000000 0.8529312 0.6165373
    ## rf        0.6575943 0.7871779 0.8529312 1.0000000 0.6842660
    ## gbm       0.4584819 0.6679394 0.6165373 0.6842660 1.0000000
