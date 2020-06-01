# cousera_final
library(caret)
library(rpart)
library(rpart.plot)
library(RColorBrewer)
library(rattle)
library(randomForest)
library(knitr)
library(plyr)
install.packages('rattle')

# load the data from URL

trainUrl <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testUrl <- "http://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
train_data<-read.csv(url(trainUrl))
test_data<-read.csv(url(testUrl))

# now let's split the testing data into training and testing : we use 70% traning 30% testing to avoid the overfitting of the data

set.seed(12345)

exercise_train<-createDataPartition(train_data$classe, p=0.7, list=FALSE)
train=train[exercise_train, ]
testing=train[-exercise_train, ]
dim(training)
dim(testing)

class(train_data)
dim(train_data)
summary(train_data)
# Clean the data : the first column is non revelant
train=train[c(-1)]
testing=testing[c(-1)]

  # there's a lot of "empty" variable, let's put away those away is they have NA in their data
train=train_data[,colSums(is.na(train_data))==0]
final_testing=test_data[,colSums(is.na(test_data))==0]








dim(training)
str(training)

dim(final_testing)
str(final_testing)

  #also, we need to remove variable of no interest such as the one with near zero variance as they can't be important

nzv=nearZeroVar(train)
train<-train[,-nzv]

testing<-testing[,-nzv]
dim(train)
dim(testing)


/*Let's test the first model : the decision tree. We are, wit this model, looking for the variables 
that predicts the best the class for each data
*/
set.seed(12345)
model_rf <- randomForest(classe ~ ., data=train)
prediction_rf <- predict(model_rf, testing, type = "class")
cmrf <- confusionMatrix(prediction_rf, testing$classe)
cmrf

/* to assess each variable randomForest function returns a certains number of information
confusion matrix : were the ligns are the observed data of the algorithm and on the column the predicted data. 
In our example, there are no people misclassified by the algorithm. 
we can plot to see the result and order the variable in order of importance */

plot(model_rf)
model_rf$confusion
varImpPlot(model_rf)
model_rf$importance[order(model_rf$importance[, 1], decreasing = TRUE), ]

# we see that the two main criteria are cvtd timestamp and raw timestamp

plot(classe ~ cvtd_timestamp, data = testing)
plot(classe ~ num_window, data = testing)



/* now we can try the same method but with a more powerfull caret library
the caret library allows use to tune in our model, and tries the algortihm for 
the number of tree
the number of variable tested each iteration
we were on default for the random forest function.*/



set.seed(123)
library(caret)
model_caret <- train(classe ~ ., data = training, method = "rf")
print(model_caret)
print(model_caret$finalModel)
varImpPlot(model_caret$finalModel)

varImp(model_caret)
plot(varImp(model_caret), top = 10)

/* to conclude on the first method on random forest, we find an Accuracy of 1 and Kappa : 1 which means a perfec argument. It is so perfect that it can be
problematic as the data could be overfitting */

# Method n2 : now let's try to see how accurate we are with classification tree
/*  the method relies on classify ing the new information into one of the categories of the old data 
it's a probabilistic estimate of class membership */

set.seed(12345)
model_decisionTree <- rpart(classe ~ ., data=training, method="class")
fancyRpartPlot(model_decisionTree)
# we test it on test data 

predictTree <- predict(model_decisionTree, testing, type = "class")
model_tree <- confusionMatrix(predictTree, testing$classe)
model_tree

/* we see an  Accuracy : 0.8768   and  Kappa : 0.844, which are both a lot lower than the random forest prediction. the out of the bag 
information rate is 0.2831 so quite high. It is not as good as Random forest algorithm */

/* Method 3 : now let's try the GBM model. The idea behind it 
GBMs build an ensemble of successive treesthat learnt on the previous tree, instead on building many different tree like rf */

set.seed(12345)
control_GBM <- trainControl(method = "repeatedcv", number = 10, repeats = 1)
model_GBM  <- train(classe ~ ., data=training, method = "gbm", trControl = control_GBM, verbose = FALSE)
model_GBM$finalModel
print(model_GBM)

  #test  the GBM model and accuracy

predict_GBM <- predict(model_GBM, newdata=testing)
cmGBM <- confusionMatrix(predict_GBM, testing$classe)
cmGBM

/*   Accuracy : 0.9995 and  Kappa : 0.9994 . A lot better than simple decision tree but not as much as random forest */

# Decision : the Random forest method is the best even with doubt of overfitting. Now let's apply rf to the final_testing model


final_t<-data.frame(final_testing)
Results <- predict(model_rf, newdata=final_t)
Results
