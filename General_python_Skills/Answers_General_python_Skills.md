
# General_python_Skills

## Questions:

#### -Which models did you chose for each problem? Why?

##### Case 1 - Classification

For this case I have choosen a Naive Bayes model because the size of the dataset given is not very big and the classifications are equially distributed.

##### Case 2 - Regression


For this problem initially I have choosen a Lineal regression model due to how the data is dustributed, but to be sure I have compared the initial Lineal model I have created a Polynomial regression model where i achieved a slightly better results than the lineal one.


#### -Regarding the evaluation, which metrics are you using for the classification problem? Why are you using them and how do you interpret the results? 

For the classification case one initially I have used the Accuracy that gives you a global accuracy of how close all the predictions where made (0-1, being 0 nothing predicted at all and 1 fitting perfectly the prediction with the actual data). 
I also used the confusion matrix to see in more detail how the predictions has bahave and understand better the false positives that would appear.
And I also used a classification_report function that shows the individual accuracy of each class.


#### -And for the regression?

For the regression case I have used the R2 metric that shows how correlated is the prediction with the actual data (0-1, being 0 nothing predicted at all and 1 fitting perfectly the prediction with the actual data). 
And the MSE that is the average of the square of the errors, meaning that calculate how different is the prediction from the actual data, the higher the error is bigger and then the prediction worst, it is very useful to compare with other models.


#### -About the plot. How do you interpret the results? Which kind of relation is shown? Is it enough to take some conclusions or do we need to perform further analysis?

We can apreciate that when the price (MEDV, x$1000) decrease when the Lower Status of the population increases (LSTAT, %), meaning that the price of the houses decreases when the Status of the population decreases. 
We can see a quite high correlation between this 2 variables (pearsons coef = -0.74), but it is true that we can not definitively conclude anything form only this data since correlation not always imply causation and further analysis would be needed, comparing data from other sources, performing experiments (A/B testing) etc.


![graph](https://github.com/Yoshe1101/Antai_data_Analyst_test/tree/master/General_python_Skills/img/1.png)
