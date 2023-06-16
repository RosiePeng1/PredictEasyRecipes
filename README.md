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

Model: **Random Forest**
- As the improvement of decision tree model, random forest classification model applies bootstrapped samples, and it solves the issue of correlation between trees due to different strength across predictors. According to the exploratory analysis, there are possible stronger predictors (e.g. `minutes`) than others, so I choose random forest model to perform the prediction. 
- For hyperparameters, I use default setting for most of them, except tuning `n_estimators` to be 50. This is due to the consideration on runtime, and this number is not too small to provide an appropriate result.

Features: `minutes`, `n_steps`, `n_ingredients`
- The three variables are all quantitative.
- `minutes` refers to the minutes needed to cook a recipe; `n_steps` and `n_ingredients` refer to the number of steps and ingredients in a recipe respectively. More description about variables can be found in exploratory analysis. 
- To deal with some recipes which take extreme long time to cook, I encode `minutes` variable into a binary indicator, describing if the recipe takes over 3 hours to cook. 

Performance: The precision on unseen data is 0.7501. I believe this score is not bad, but there's space for improving. It is plausible to add more features in the model. Also, random forest is affected largely by its hyperparameters, so I can do more works on optimizing some of them to improve the model.

Below is the confusion matrix for the baseline model on unseen data. 
<iframe src="assets/baseline.png" width=450 height=450 frameBorder=0></iframe>

---

## Final Model



## Fairness Analysis


