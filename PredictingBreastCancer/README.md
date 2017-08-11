# Predicting breast cancer from tumor characteristics

### Background information

Biopsy via fine-needle aspiration is a relatively non-invasive and quick technique for assessing the malignancy of breast lumps ([source](https://bmccancer.biomedcentral.com/articles/10.1186/1471-2407-12-41)). Breast cancer affects one in eight women ([source](http://www.breastcancer.org/symptoms/understand_bc/statistics)), and early detection is critical for safe recovery ([source](http://www.cancerresearchuk.org/about-cancer/cancer-symptoms/why-is-early-diagnosis-important)). 

### Objective

Accurately predict whether a breast tumor is benign or malignant based on its characteristics (*e.g.*, size, texture, etc.).

### Machine learning process

Using standardized tumor characteristics, I selected the eight most prominent predictors for classification modeling. I then tested a diverse set of machine learning algorithms to assess which models would best fit the data and fine-tuned the parameters for each model. At this stage, the model with the best prediction (0.9696 cross-validation accuracy) was a gradient boosting machine that allowed for 2-way interactions between predictor variables. I then tried model stacking with a random forest algorithm to see whether I could improve the accuracy. This stacked model included a generalized linear model (glm), radial support vector machine (svmRadial), random forest model, a C5.0 model, and the gradient boosting machine. The prediction was slightly better compared with the fine-tuned gradient boosting machine with a cross-validation accuracy of 0.977. All analyses were conducted in R using the caret package ([Kuhn](https://CRAN.R-project.org/package=caret)). 

### Results and interpretation

Modeling shows that a only a modest number (eight) of breast tumor/lump characteristics are needed to accurately assess whether the tumor is benign or malignant. This machine learning approach could be applied to help detect and diagnose breast cancer in hospitals and clinics. 

### Next steps



Ideas for dashboard:

Breast cancer prevalence [data](http://www.breastcancer.org/symptoms/understand_bc/statistics)

PCA with vectors for breast cancer lumps, colored by benign and malignant

CV accuracy for machine learning model 
* interpretation --> which characteristics are needed for proper diagnosis?
* next steps...

### Description of files:
