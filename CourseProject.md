Practical ML Course Project
========================================================
Executive Summary
--------------------
The goal of this project is to predict the manner in which the exercise was done as described at the pml-training dataset from http://groupware.les.inf.puc-rio.br/har. This is the "classe" variable in the training set.  
Here we explain how was built the model (Random Forest model), the accuracy obtained (0.997) and the results predicting 20 different test cases at pml-testing dataset.

Preparing the dataset
-----------
Load de dataset:

```r
data <- read.csv("pml-training.csv")
```

Remove NAs an empty columns:

```r
data[data == ""] <- NA
data <- data[, colSums(is.na(data)) == 0]
```

All the variables that seems not useful for our prediction model, are also removed:

```r
removeIndex <- grep("timestamp|X|user_name|new_window", names(data))
data <- data[, -removeIndex]
```

Creating training and test datasets
-----------
As the dataset is big enough, and, in order to optimize the fit of our model , we begin taking 50% of data for training purpose, and 50% for testing purpose:

```r
trainIndex <- createDataPartition(y = data$classe, p = 0.5, list = FALSE)
training <- data[trainIndex, ]
testing <- data[-trainIndex, ]
```

We got a training dataset with 9812 observations and a testing dataset with 9810 observations.
If with this partition we don't get a satisfactory accuracy in our model, we can increase later the training dataset.

Fitting the model
-------------------
With the 53 predictors remaining, we are going to fit a random forest model, to see if the accuracy obtained is enough or we have to look for another strategy:

```r
modFit <- train(training$classe ~ ., data = training, method = "rf")
```

Testing the model
--------------------
We test our model with the test set "testing":

```r
pred <- predict(modFit, testing)
```

Obtaining these results:

```r
confusionMatrix(pred, testing$classe)
```

    Confusion Matrix and Statistics

              Reference
    Prediction         A    B    C    D    E
                  A 2789   12    0    0    0
                  B    0 1882    7    0    0
                  C    0    4 1704    4    0
                  D    0    0    0 1604    3
                  E    1    0    0    0 1800

    Overall Statistics

    Accuracy : 0.9968          
    95% CI : (0.9955, 0.9979)
    No Information Rate : 0.2844          
    P-Value [Acc > NIR] : < 2.2e-16       

    Kappa : 0.996           
    Mcnemar's Test P-Value : NA              

    Statistics by Class:

                        Class: A Class: B Class: C Class: D Class: E
    Sensitivity            0.9996   0.9916   0.9959   0.9975   0.9983
    Specificity            0.9983   0.9991   0.9990   0.9996   0.9999
    Pos Pred Value         0.9957   0.9963   0.9953   0.9981   0.9994
    Neg Pred Value         0.9999   0.9980   0.9991   0.9995   0.9996
    Prevalence             0.2844   0.1935   0.1744   0.1639   0.1838
    Detection Rate         0.2843   0.1918   0.1737   0.1635   0.1835
    Detection Prevalence   0.2855   0.1926   0.1745   0.1638   0.1836
    Balanced Accuracy      0.9990   0.9953   0.9975   0.9986   0.9991
    
Obtaining an accuracy of 0.997

Using the model
--------------------
With this model, we predict the 20 test cases:

```r
pml.testing <- read.csv("pml-testing.csv")
test.pred <- predict(modFit, pml.testing)
```

Obtaining a 100% of correct answers at 20 test cases:

```r
test.pred
```

    [1] B A B A A E D B A A B C B A E E A B B B
    Levels: A B C D E
