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


## Baseline Model



## Final Model



## Fairness Analysis


