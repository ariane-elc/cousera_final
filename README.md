
1. load the data from URL



2. now let's split the testing data into training and testing : we use 70% traning 30% testing to avoid the overfitting of the data


Clean the data : the first column is non revelant


there's a lot of "empty" variable, let's put away those away is they have NA in their data


also, we need to remove variable of no interest such as the one with near zero variance as they can't be important




# 1 Let's test the first model : the decision tree. We are, wit this model, looking for the variables that predicts the best the class for each data


to assess each variable randomForest function returns a certains number of informationconfusion matrix : were the ligns are the observed data of the algorithm and on the column the predicted data. In our example, there are no people misclassified by the algorithm we can plot to see the result and order the variable in order of importance

we see that the two main criteria are cvtd timestamp and raw timestamp



now we can try the same method but with a more powerfull caret library. the caret library allows use to tune in our model, and tries the algortihm for the number of treethe number of variable tested each iteration we were on default for the random forest function.




to conclude on the first method on random forest, we find an Accuracy of 1 and Kappa : 1 which means a perfec argument. It is so perfect that it can beproblematic as the data could be overfitting 

# 2. Method n2 : now let's try to see how accurate we are with classification tree. the method relies on classify ing the new information into one of the categories of the old data, it's a probabilistic estimate of class membership.

 we test it on test data 



 we see an  Accuracy : 0.8768   and  Kappa : 0.844, which are both a lot lower than the random forest prediction. the out of the bag information rate is 0.2831 so quite high. It is not as good as Random forest algorithm.

# 3. Method 3 : now let's try the GBM model. The idea behind it GBMs build an ensemble of successive treesthat learnt on the previous tree, instead on building many different tree like rf.



 test  the GBM model and accuracy

Accuracy : 0.9995 and  Kappa : 0.9994 . A lot better than simple decision tree but not as much as random forest */

# Decision : the Random forest method is the best even with doubt of overfitting. Now let's apply rf to the final_testing model



