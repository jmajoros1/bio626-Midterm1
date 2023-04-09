# bio626-Midterm1
Final repository containing code used for training, evaluation, and generating results of test data. 

In order to reproduce the results, follow the instructions for each of the binary and multi classification methods listed below. 

**Data processing:**
- Read in the two text files with the read.table() function with header = TRUE. I set each of these equal to training_data and test_data respectively. 
- For each classification task, I separated the training data into a subset of the predictors and the response. This is done by making a new data set (I named training_data.X) that is equal to the training_data without the first two columns (subject and activity).
- Further data processing for each task is explained below in each task section. 

**Binary Classification:**
- In order to make the response binary, create a new variable (I named training_data.Y) that assigns a value of 1 when the activity is [1,2,3], and 0 otherwise. This creates the distinciton between static and dynamic movements. 
- The method of choice here was a support vector machine with a radial kernel. Make sure the e1071 package is installed and loaded in your environment. 
- Now you want to train the svm on the established training data. This is done with this code: 

```
svmfit_rad = svm(as.factor(training_data.Y) ~ ., 
        data = training_data.X, 
        kernel = "radial", 
        scale = FALSE, 
        cost = 10)
```

**Multiple Classification:**
