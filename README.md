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

- The method of choice here was a support vector machine with a radial kernel. Make sure the e1071 package is installed and loaded in your environment. 
- Now you want to train the svm on the established training data. This is done with this code: 

```
svmfit_rad = svm(as.factor(training_data.Y) ~ ., 
        data = training_data.X, 
        kernel = "radial", 
        scale = FALSE, 
        cost = 10)
```

- In order to visualize the results, predict the results for the test data. This is done with the code below. 

```
svm.pred_rad = predict(svmfit_rad, test_data,
                   decision.values = T)
```

**Multiple Classification:**
