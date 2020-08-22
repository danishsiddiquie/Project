Movie Recommendation: Collaborative filtering
================

1. Convert data set into binary values
--------------------------------------

First convert the ratings of both the training and test sets into binary data - 0 if the user has not rated that movie, 1 if they have rated it.

``` r
train <- read.csv("train_movies.csv")
train[is.na(train)] <- 0
train[,2:length(train)][train[,2:length(train)] >0] <- 1
train

write.csv(train, "new_train.csv")
```

3.Evaluate best knn algorithm parameters
----------------------------------------

Now I use the ‘knn’ method of caret to construct the k nearest neighbors model predicting if a user has watched TPB (movie Id 1197) on the training set for a fixed k, say 25. To cut down on computation time, I use only 5-fold cross validation

``` r
train = read.csv("new_train.csv")
train_data = train[,3:length(train)]
train_data$mId1197 <- as.factor(train_data$mId1197)

#Simple call of k-nearest neighbors, specifying k=25
trctrl = trainControl(method = "repeatedcv", number = 5, repeats = 1)

start = Sys.time()
knn_fit = train( mId1197 ~., 
                   data = train_data, 
                   method = "knn",
                   preProcess = c("center", "scale"),
                   trControl = trctrl,
                   tuneGrid=expand.grid(k=25))
end = Sys.time()
end-start

start = Sys.time()
knn_fit = train( mId1197 ~., 
                   data = train_data, 
                   method = "knn",
                   preProcess = c("center", "scale"),
                   trControl = trctrl,
                   tuneGrid=expand.grid(k=15))
end = Sys.time()
end-start
```

5.607492 mins for k = 25 5.565944 mins for k = 15

We can see that there is not much difference in time when k=15 or k=25. Thus, it would be safe to say that the time taken for the knn function to execute for any value of k would be the same, and it would make more sense to user a higher k value for better accuracy without sacrificing efficiency.

``` r
knn_fit2 = train( mId1197 ~., 
                   data = train[,3:length(train)], 
                   method = "knn",
                   preProcess = c("center", "scale"),
                   trControl = trctrl,
                   tuneLength = 15)
plot(knn_fit2)
```

best result with k = 7

4. Prediction function (Confusion Matrix) using knn model built
===============================================================

``` r
knn_fit3 = train( mId1197 ~., 
                   data = train[,3:length(train)], 
                   method = "knn",
                   preProcess = c("center", "scale"),
                   trControl = trctrl,
                   tuneGrid=expand.grid(k=7))

test_pred = predict(knn_fit3, new_data = train_data)
confusionMatrix(test_pred, train$mId1197, positive="1")
```

Accuracy: predicts overall, how often is our model correct? I.e. how often would the model correctly make decisions to send /not send ads for princess bride to potential viewers. Value: 0.7367

Accuracy is quite good, around 74% correct decisions made by the model on the training set.

Sensitivity: When viewer actually wants to see the movie, how often does our model predict that the viewer wants to watch it? Value: 0.4886

The sensitivity value is very important to us, as we want to correctly identify viewers who want to watch the princess bride, so that the ad results in the user watching the movie, thus resulting in profit. Such as low sensitivity value would mean that the model is not correctly able to identify that the viewer wants to watch the movie, and thus we are underestimating the no. of people who want to watch the movie. In our case, sending more ads than viewers would still be better than under sending ads to potential princess bride viewers.

Specificity: When viewer does not want to see the movie, how often does our model predict that the viewer does not want to see the movie? Value: 0.94

The specificity value is not as important to us as the sensitivity value, primarily because in our context, overestimating the result may not be such a drastic problem. Overestimation is inevitable in advertisement as we would want to reach as much people as possible. Nevertheless, we do want a good enough value for specificity, as we do not want to spend so much money on ads that we end up with more cost than profit.

The kappa is essentially a measure of how well the classifier performed as compared to how well it would have performed simply by chance. In other words, a model will have a high Kappa score if there is a big difference between the accuracy and the null error rate. Value: 0.4502

The value of kappa isn’t very high, nor is it extremely low.

In the context of our advertisement for the princess bride, the model is not as good as we would hope for, primarily because of the low sensitivity value. We cannot lose out on potential customers who will want to watch the movie, and thus sending the ad to them is crucial. A high sensitivity score with a mediocre specificity score will result in a high True Positive (Which is a must), and mediocre True Negative, which is not our main concern since as we previously stated, minor overestimation is common in advertisement.

5
=

I'm not happy with the performance, primarily because by default model training in Caret maximizes accuracy. We can do better by having caret return us the fitted probabilities: first use ‘type=“prob” ’ as an argument to the predict function of caret to extract the probabilities that each training user will watch TPB. For a range of reasonable cutoffs, use these to make a table of TP, FP, TN, and FN on the test sample if we use each cutoff

``` r
#Create Predicted Probabilities 
train_data = train_data %>% select(-mId1197)
y <- train$mId1197

predicted_probability = predict(knn_fit3, newdata = train_data,type='prob')
watch_probability = predicted_probability[,2]

performance = setNames(data.frame(matrix(ncol = 9, nrow = 201)), c("Cutoff","TN", "FN", "TP", "FP", "Sensitivity", "Specificity","Accuracy","profit"))
performance$Cutoff = seq(0.1,0.3,.001)

for (i in 2:200){
  temp = table( watch_probability > performance$Cutoff[i], y)
  TN = temp[1,1]
  FN = temp[1,2]
  FP = temp[2,1]
  TP = temp[2,2]
  performance$TN[i] = TN
  performance$TP[i] = TP
  performance$FN[i] = FN
  performance$FP[i] = FP
  performance$Sensitivity[i] = TP/(FN+TP)
  performance$Specificity[i] = TN/(TN+FP)
  performance$Accuracy[i] = (TP+TN)/(FP+FN+TP+TN)
  performance$profit[i] = -0.2*(TP + FP) + 0.5*(TP)
}
```

Some cat!
---------

<center>
<img src="https://github.com/danishsiddiquie/Project/blob/master/Movies%20Project/movies-2/lab5_files/figure-markdown_github/q6.png">
</center>
6
=

In our table, we added another column of profit, which we calculated using:

Profit = 0.5*TP-0.2*(TP+FP)

I.e. Revenue = the revenue per movie multiplied by the amount of people who did end up seeing the movie, and Cost = cost per ad multiplied by the total number of users we sent the ad to. Moreover, we send the advertisement to everyone we predicted as 1 in the confusion matrix. \*Please note that the profit only takes into consideration the users who watched the movie based on the ad, and not randomly. Thus, we do not take into consideration the False Negatives, i.e. people who we did not think will see the movie but saw the movie anyway.

Looking at the table we created for cutoff values between 0.1 and 0.3, we saw that we gained the most profit on our training data ($607.4) when using the cutoff value of 0.1. The result makes sense since it has a high sensitivity value and a good specificity value, resulting in maximum number of people getting the ad to the people who would watch the movie, and a little overestimation due to the mediocre specificity, which is not that big of a deal, considering that our True Positives are maximized in this manner.

7
=

``` r
library(dplyr)
test <- read.csv("test_movies.csv")
test[is.na(test)] <- 0
test[,2:length(test)][test[,2:length(test)] >0] <- 1
test <- test[2:length(test)]
test$mId1197 <- as.factor(test$mId1197)
y <- test$mId1197
test<- test%>%select(-mId1197)
predicted_test = predict(knn_fit3, newdata = test, type='prob')
test_probability = predicted_test[,2]

delta = 0.1
predicted_watch = ifelse(test_probability >= delta,1,0) #Class prediction
predicted_watch <- as.factor(predicted_watch)
confusionMatrix(predicted_watch,y, positive="1") #Create confusion matrix
```

Some cat!
---------

<center>
<img src="https://github.com/danishsiddiquie/Project/blob/master/Movies%20Project/movies-2/lab5_files/figure-markdown_github/q7.png">
</center>
Using the analysis, explanation, and assumption from Q 4 and 6, these are the results of our model on the test data:

1.  Out of 1982 people we sent the ad to, 1191 people were true positives, i.e who we sent the advertisement to and who end up watching the movie Princess Bride.

2.  Profit = 1191*0.5 – 0.2*1982 = $ 199.1

If ad was sent to everyone (3000 total users, out of which 1399 users watched (True positives + False Positives):

Profit = 1399*0.5 – 0.2*3000 = $ 99.5

1.  791 users is the number of false positives that we got from our model, i.e. we predicted they will see the movie and thus advertised to them, but they did not end up renting it. Therefore: cost = 791\*0.2 = $ 158.2

2.  208 people were false negatives. Which means after a week, 208 people who we predicted will not see the movie (and thus we did not send the advertisement to) still ended up watching the movie.
