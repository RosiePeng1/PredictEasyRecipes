# Predict: Will a Recipe be Easy to Make?
Project 5 for DSC 80 (Spring 2023) by Rosie Peng (q1peng@ucsd.edu).

The exploratory data analysis on this dataset is [here](https://rosiepeng1.github.io/Easy-Recipes-Analysis/).

## Framing the Problem

**Prediction problem:** 

**predict if a recipe is easy to make.**

This is a binary classification problem. In the dataset, some recipes have tag containing "easy", and then they are considered to be easy; other recipes which don't have a tag related to "easy" are considered difficult, or say not easy. 

The response variable will be `easy`. It a binary variable which represents whether the recipe has tag "easy" or not. 

The metric I'm using to evaluate the model is **precision**. I choose this metric since false positive (predict a not easy recipe as easy) is relatively more severe than false negative in this case. You may imagine that a person who search for easy recipes could be new to cook or don't have enough cook skills, and these viewers will not be able to follow difficult recipes; but people who are confident with hard recipes will also perform well on easy ones. According to this consideration, I decide to use precision as metric to value the importance of false positive.

The features I would know at the "time of prediction" are the number of steps and ingredients, nutritions, minutes needed to make, and the average rating of the recipes, and they are the features I will use in the following model(s).

---

## Baseline Model

##### Model: 

**Random Forest**
- As the improvement of decision tree model, random forest classification model applies bootstrapped samples, and it solves the issue of correlation between trees due to different strength across predictors. According to the exploratory analysis, there are possible stronger predictors (e.g. `minutes`) than others, so I choose random forest model to perform the prediction. 
- For hyperparameters, I use default setting for most of them, except tuning `n_estimators` to be 50. This is due to the consideration on runtime, and this number is not too small to provide an appropriate result.

##### Features:

 `minutes`, `n_steps`, `n_ingredients`
- The three variables are all quantitative.
- `minutes` refers to the minutes needed to cook a recipe; `n_steps` and `n_ingredients` refer to the number of steps and ingredients in a recipe respectively. More description about variables can be found in exploratory analysis. 
- To deal with some recipes which take extreme long time to cook, I encode `minutes` variable into a binary indicator, describing if the recipe takes over 3 hours to cook. 

##### Performance: 

The precision on unseen data is 0.7501. I believe this score is not bad, but there's space for improving. It is plausible to add more features in the model. Also, random forest is affected largely by its hyperparameters, so I can do more works on optimizing some of them to improve the model.

Below is the confusion matrix for the baseline model on unseen data. 
<iframe src="assets/baseline.png" width=800 height=600 frameBorder=0></iframe>

---

## Final Model

##### Model

The model I use is still Random Forest. Comparing to the baseline model: 
- I use `GridSearchCV` to find the combination of hyperparameters with the best performance by K-Fold Cross-Validation. The number of fold I choose is 6. The scoring method is precision. 
- For the parameters to be tuning, I focus on `n_estimators`, `max_depth`, and `max_features`. The number of estimators(trees) and max depth of each decision tree affect the prediction result largely, and they are usually the key points of tuning. `max_features` defines the way we limit the max number of features used in a decision tree in the random forest, which is a core element of the uniqueness of this model. 
- The best parameters combination is `n_estimators=50`, `max_depth` is default `None` (which means nodes are expanded until all leaves contain less than `min_samples_split` samples), and `max_features` is `sqrt` (the number of features to consider is the square root of number of features we have). 
- To decide the model, I also tried Logistic Regression model, but it has a worse performance than random forest. As a result, I use random forest model as the final decision. 

##### Features

In the final model, I add the following features from dataset and encodings on existing features:
- `calories` is created from the `nutrition` variable of our data, representing the calories a recipe has, and it's standardized.
- `avg_rating` is added in the model as a numerical variable. The missingness in it are filled by 0, as they occur because the recipe has no rating at all. 
- The extreme long cooking time indicator from `minute` is kept from the baseline model. Also, `minute` is standardized.
- For `n_steps` and `n_ingredients`, one thing I did is create 5 ordinal quantile features for each of them. Another thing is I create binary indicators for them to express if the recipe contains only a small number (less than 5) of steps/ingredients, because by exploratory analysis, most recipes with small number of steps or ingredients are easy. 

##### Performance

The precision on unseen data is 0.9348, which is a very high evaluation. Comparing to the precision of about 0.75, the precision of my model is improved obviously. Also, the false positive (which I concern most) decreases from ~8000 to ~2000 on the same set of unseen data. 

Below is the confusion matrix for the final model on unseen data:
<iframe src="assets/final.png" width=800 height=600 frameBorder=0></iframe>


## Fairness Analysis

In this part, the question I will try to answer is: 

Does my model perform better for recipes with high average rating (over 4 points) than it does for recipes with low average rating (equal or less than 4 points)?

I choose the average rating to group the predictions because the false positive rate (1-precision) looks suspicious for different level of average rating. You can observe this in the graph:

<iframe src="assets/FP_avgrating.html" width=800 height=600 frameBorder=0></iframe>

To answer this question, I will perform a **permutation test** to check if predictions in high-rating group and those in low-rating group are likely to be from the same distribution.

I will generate the binary indicator of high/low average rating from `avg_rating`. This column contains some missingness, which indicate that the recipe does not have any rating. I use 0 to fill these missingness when perform prediction.

The test statistic will be difference between precisions (high - low), and the significance level is 0.01.  

##### Hypothesis

Null Hypothesis: Our model is fair. Its precision for high-rating recipes and other recipes are overall same.

Alternative Hypothesis: Our model is unfair. Its precision for high-rating recipes is *higher* than its precision for low-rating recipes.

##### Result & Conclusion

The p-value is 0, which means the null hypothesis is rejected at the significance level of 0.01 (actually, at any usually-used significance level, 0.01, 0.05, or 0.1). **There is enough evidence that the model is unfair across high-rating and low-rating recipes.** This could happens because of recipes with no ratings are counted as have an average rating of 0, and these recipes draw down the precision of prediction.

