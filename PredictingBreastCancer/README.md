# Predicting breast cancer from tumor characteristics

### Background information

Biopsy via fine-needle aspiration is a relatively non-invasive and quick technique for assessing the malignancy of breast lumps ([source](https://bmccancer.biomedcentral.com/articles/10.1186/1471-2407-12-41)). Breast cancer affects one in eight women ([source](http://www.breastcancer.org/symptoms/understand_bc/statistics)), and early detection is critical for safe recovery ([source](http://www.cancerresearchuk.org/about-cancer/cancer-symptoms/why-is-early-diagnosis-important)). 

### Objective

Using a breast cancer dataset, my goal it to accurately predict whether a breast tumor is benign or malignant based on its cell nucleus characteristics (*e.g.*, size, texture, etc.).

### Machine learning process

Using standardized tumor characteristics, I selected the eight most prominent predictors for classification modeling. I then tested a diverse set of machine learning algorithms to assess which models would best fit the data and fine-tuned the parameters for each model. At this stage, the model with the best prediction (0.9696 cross-validation accuracy) was a gradient boosting machine that allowed for 2-way interactions between predictor variables. I then tried model stacking with a random forest algorithm to see whether I could improve the accuracy. This stacked model included a generalized linear model (glm), radial support vector machine (svmRadial), random forest model, a C5.0 model, and the gradient boosting machine. The prediction was slightly better compared with the fine-tuned gradient boosting machine with a cross-validation accuracy of 0.977. All analyses were conducted in R using the caret package ([Kuhn](https://CRAN.R-project.org/package=caret)). 

### Results and interpretation

Modeling shows that a only a modest number (eight) of breast tumor/lump characteristics are needed to accurately assess whether the tumor is benign or malignant. This machine learning approach could be applied to help quickly detect and diagnose breast cancer in hospitals and clinics. 

### Next steps

To make this machine learning approach easy to use and incorporate in hospitals, we would need to create a web application that extracts the eight necessary characteristics from breast biopsy images (e.g., cell nuclei mean area, concavity, texture). With this approach, the measurements would be (1) standardized across samples, (2) measurement/human error would hopefully be eliminated, and (3) measurements would be fast and easy to procure and automate. The application would then feed the data into the stacked machine learning model and produce a clear outcome: malignant or benign. In addition, the application could also deliver more information about the breast lump in comparison to the larger population (e.g., what percentile does the lump belong in for the different measurements compared to a database of breast lumps?).

---
### Description of files:

* Predicting breast cancer files = contains figures that are linked to the "PredictingBreastCancer.md" report
* BreastCancerData.csv = downloaded dataset from [Kaggle](https://www.kaggle.com/uciml/breast-cancer-wisconsin-data)
* PredictingBreastCancer.Rmd = R markdown document used to create the "PredictingBreastCancer.md" report
* **PredictingBreastCancer.md** = report that outlines the data exploration, processing, modeling, and results (includes figures)
* PredictingBreastCancer_dashboard.Rmd = R markdown document used to create the "PredictingBreastCancer_dashboard.Html" interactive report
* **PredictingBreastCancer_dashboard.Html** = interactive and visual report of the project (since this is a large file, you will need to download and open it to view the file)
* data.csv = smaller dataset used to make interactive visuals in the "PredictingBreastCancer_dashboard.Html" interactive report
