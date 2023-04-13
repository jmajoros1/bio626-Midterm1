# bio626-Midterm1
Final repository containing code used for training, evaluation, and generating results of test data. 

In order to reproduce the results, follow the instructions for each of the binary and multi classification methods listed below. 

**Data processing:**
- Read in the two text files with the read.table() function with header = TRUE. I set each of these equal to training_data and test_data respectively. 
- For each classification task, I separated the training data into a subset of the predictors and the response. This is done by making a new data set (I named training_data.X) that is equal to the training_data without the first two columns (subject and activity). This also formats the training data in the same format that the test data is in. 

```
training_data.X = training_data[-c(2,ncol(training_data))]
```

- Further data processing for each task is explained below in each task section. 

**Binary Classification:**
- In order to make the response binary, create a new variable (I named training_data.Y) that assigns a value of 1 when the activity is [1,2,3], and 0 otherwise. This creates the distinciton between static and dynamic movements. 

```
training_data.Y = ifelse(training_data$activity == 1 | training_data$activity == 2 | training_data$activity == 3, 1, 0)
```

- The method of choice here was utilizing a committee approach with logistic regression, linear discriminant analysis, and a support vector machine. Make sure the e1071 package is installed and loaded in your environment. 
- Now you want to train each of the models on the established training data and get the predicted values. This is done with this code: 

For Logistic Regression:
```
fit1_log = glm(training_data.Y ~., 
               data = training_data.X, 
               family = binomial)

fit1_prob = predict(fit1_log, test_data, type = "response")
fit1_pred = rep(0, dim(test_data)[1])
fit1_pred[fit1_prob > 0.5] = 1
```

For LDA:
```
fit2_lda = lda(training_data.Y ~ ., 
               data = training_data.X)

fit2_pred = predict(fit2_lda, test_data)$class
fit2_pred = as.numeric(unlist(fit2_pred)) - 1
```

For SVM:
```
svmfit_rad = svm(as.factor(training_data.Y) ~ ., 
        data = training_data.X, 
        kernel = "radial", 
        scale = FALSE, 
        cost = 10)

svm.pred_rad = predict(svmfit_rad, test_data,
                   decision.values = T)
```

- Now that you have the predictions for each method, you want to hold a vote amongst the three, where the majority classification choice will be the final classified choice. This is done so as below: 

```
vote_vec = fit1_pred + fit2_pred + svm.pred_rad
ensemble.pred = rep(0, length(vote_vec))
ensemble.pred[vote_vec >= 2] = 1
ensemble.pred = ifelse(is.na(ensemble.pred), 0, 1)
ensemble.pred
```

- The ensemble.pred is the final output for all of the methods from the committee process. 

**Multiple Classification:**
- Just as the response for binary classification was created, we do the same thing for multi class classification here. We will create a new response variable (I named training_data.Y2) that keeps the activity value provided except for static postural changes. This is done so below.

```
training_data.Y2 = training_data$activity
  
training_data.Y2 = ifelse(training_data.Y2 == 7 | training_data.Y2 == 8 | training_data.Y2 == 9 |training_data.Y2 == 10 | training_data.Y2 == 11 | training_data.Y2 == 12, 7, training_data.Y2)
```

- A support vector machine was the method of choice for the multi-class classification task. In order to train the svm and predict each of the classes, follow the code below:

```
svm_fit_multi = svm(as.factor(training_data.Y2) ~ ., 
        data = training_data.X2, 
        kernel = "radial", 
        scale = FALSE, 
        method = "C-classification",
        cost = 10)

svm.pred_multi = predict(svm_fit_multi, test_data,
                   decision.values = T)
```

- The svm.pred_multi object is the final list of classification results for the multi-class classification task. 
       
