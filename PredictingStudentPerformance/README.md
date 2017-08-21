# Predicting student performance

### Background information

### Objective
Using a student achievement dataset, accurately predict a student's final math score based on the student's characteristics (e.g., time spent studying, mother's education level, involvement in extra-curricular activities).

### Machine learning process
With feature selection, I found that all 30 standardized student characteristics were needed to obtain the most accurate predictions of the students' final scores. I then tested out various types of regression algorithms to assess which algorithms were best at minimizing the cross-validation root mean squared error (gradient boosting machine, random forest, bagged regression tree, and a recursive partitioning and regression tree all predicted student performance fairly well). I then tuned the models and selected the models that gave the best predictions and were fairly uncorrelated (< 0.75) with one another, resulting in the random forest and recursive partitioning regression tree models. Lastly, I used model stacking of these two models, stacked with a gradient boosting machine. This final model resulted in a cross-validation root mean squared error of 0.81 which was slightly lower than individual models (>= 0.84) and much better than the results from published studies using the same dataset (Cortez and Silva 2008, best RMSE was 1.75). 

### Results and interpretation
Although I was able to minimize prediction error, it is evident that these 30 student characteristics are ok but not great at predicting the student's performance. The R<sup>2</sup> for the best model was only 0.34. Yet, this machine learning approach does help us understand which of the 30 student characteristics are most important in predicting their performance: number of failures, mother and father's education level, and whether the student intends to seek higher education. As an educator, the number of failures a student experiences (which is the most important factor affecting their final score) could be changed through teaching that promotes student motivation and a cooperative and open learning community.

![alt text](https://github.com/HLBarker/MachineLearningPortfolio/blob/master/PredictingStudentPerformance/PredictingStudentPerformance_files/figure-markdown_github-ascii_identifiers/rank%20features-1.png "Importance of student characteristics")

### Next steps

---
### Description of files:



P. Cortez and A. Silva. Using Data Mining to Predict Secondary School Student Performance. In A. Brito and J. Teixeira Eds., Proceedings of 5th FUture BUsiness TEChnology Conference (FUBUTEC 2008) pp. 5-12, Porto, Portugal, April, 2008, EUROSIS, ISBN 978-9077381-39-7. 
