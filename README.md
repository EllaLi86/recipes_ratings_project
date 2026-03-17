# Investigation on the Relationship Between Cooking Time and Recipe Ratings
### Author: Xinyuan(Ella) Li

## Overview
This data science project, conducted at UCSD, focus on exploring the relationship between the cooking time and rating of a recipe of the given recipe.

## Introduction

Food is a critical component of our daily lives. In our fast-paced modern world, time has become one of our most valuable commodities, and the question of how long we should spend preparing meals is a constant consideration for home cooks. While some believe that great food takes time and that longer preparation leads to better results, others seek out quick recipes that deliver maximum flavor with minimal time investment. With this tension in mind, I want to investigate the relationship between recipe ratings and the time required to prepare them. I wonder if recipes with longer preparation times receive higher ratings due to their complexity and depth of flavor, or if users prefer quicker recipes that fit seamlessly into busy schedules. To do so, I am analyzing two datasets consisting of recipes and ratings posted since 2008 on [food.com](https://www.food.com/recipe/chickpea-and-fresh-tomato-toss-51631).

The first dataset, **raw_recipes**, contains **83,782 rows**, indicating 83,782 unique recipes, with 12 columns recording the following information:

| Column | Description |
|--------|-------------|
| 'name' | Recipe name |
| 'id' | Recipe ID |
| 'minutes' | Minutes to cook this recipe |
| 'contributor_id' | User ID who submitted this recipe |
| 'submitted' | Date recipe was submitted |
| 'tags' | Food.com tags for recipe |
| 'nutrition' | Nutrition information in the form [calories, total fat, sugar, sodium, protein, saturated fat, carbohydrates] |
| 'n_steps' | Number of steps in recipe |
| 'steps' | Text for recipe steps in order |
| 'description' | User-provided description |
| 'ingredients' | Text for recipe ingredients |
| 'n_ingredients' | Number of ingredients in recipe |

The second dataset, **interactions**, contains **731,927 rows** and **5 columns**, and each row contains a review from a user on a specific recipe. The columns it includes are:

| Column | Description |
|--------|-------------|
| 'user_id' | User ID |
| 'recipe_id' | Recipe ID |
| 'date' | Date of interaction |
| 'rating' | Rating given (scale 1-5) |
| 'review' | Review text |

Given the datasets, we are investigating whether there is a meaningful relationship between cooking time and how users rate recipes. To facilitate this investigation, we will analyze the **'minutes'** column from the recipes dataset to understand preparation&cooking time distributions. From the interactions dataset, we will use the **'rating'** column as our primary outcome variable, and we will calculate **'average_rating'** for each unique recipe to understand overall recipe quality as perceived by users. The most relevant columns to answer our question are **'minutes'** (preparation time), **'rating'** (individual user ratings), and **'average_rating'** (mean rating per recipe).

By seeking an answer to this question, we would gain insight into people's preferences regarding cooking time, which could help contributors on food.com tailor their recipes to match what users truly value—whether that's developing complex, time-intensive creations or streamlining their recipes for efficiency without sacrificing quality. In addition, these findings could lead to future work on recipe recommendation systems that better align with users' time constraints and preferences, potentially helping busy home cooks make more informed decisions about which recipes are worth their time.




## Data Cleaning and Exploratory Data Analysis
## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

