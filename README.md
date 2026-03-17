# Investigation on the Relationship Between Cooking Time and Recipe Ratings
### Author: Xinyuan(Ella) Li

## Overview
This data science project, conducted at UCSD, focus on exploring the relationship between the cooking time and rating of the given recipe.

## Introduction

Food is a critical component of our daily lives. In our fast-paced modern world, time has become one of our most valuable commodities, and the question of how long we should spend preparing meals is a constant consideration for home cooks. While some believe that great food takes time and that longer preparation leads to better results, others seek out quick recipes that deliver maximum flavor with minimal time investment. With this tension in mind, I want to investigate the relationship between recipe ratings and the time required to prepare them. I wonder if recipes with longer preparation times receive higher ratings due to their complexity and depth of flavor, or if users prefer quicker recipes that fit seamlessly into busy schedules. To do so, I am analyzing two datasets consisting of recipes and ratings posted since 2008 on [food.com](https://www.food.com/recipe/chickpea-and-fresh-tomato-toss-51631).

The first dataset, **raw_recipes**, contains **83,782 rows**, indicating 83,782 unique recipes, with 12 columns recording the following information:

| Column | Description |
|--------|-------------|
| 'name' | Recipe name |
| 'id' | Recipe ID |
| 'minutes' | Minutes to cook the recipe |
| 'contributor_id' | User ID who submitted the recipe |
| 'submitted' | Date recipe was submitted |
| 'tags' | food.com tags for recipe |
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

**Given the datasets, we are investigating whether there is a meaningful relationship between cooking time and how users rate recipes**. To facilitate this investigation, we will analyze the **'minutes'** column from the recipes dataset to understand preparation&cooking time distributions. From the interactions dataset, we will use the **'rating'** column as our primary outcome variable, and we will calculate **'average_rating'** for each unique recipe to understand overall recipe quality as perceived by users. The most relevant columns to answer our question are **'minutes'** (cooking time), **'rating'** (individual user ratings), and **'average_rating'** (mean rating per recipe).

By seeking an answer to this question, we would gain insight into people's preferences regarding cooking time, which could help contributors on food.com tailor their recipes to match what users truly value—whether that's developing complex, time-intensive creations or streamlining their recipes for efficiency without sacrificing quality. In addition, these findings could lead to future work on recipe recommendation systems that better align with users' time constraints and preferences, potentially helping busy home cooks make more informed decisions about which recipes are worth their time.


## Data Cleaning and Exploratory Data Analysis

To prepare the datasets for analysis, I performed several cleaning steps to ensure the data was properly formatted and ready for investigation.

**1. Merge dataframes to add `avg_rating` column**

I calculated the average rating for each recipe to understand overall recipe quality as perceived by users from `interactions` dataframe. Then, I merged two dataframes to combine recipe information with user ratings. The `merged` dataframe contains **83,782 rows** and **13 columns**.

**2: Replace rating 0.0 with NaN**

Upon inspection, I noticed some `avg_rating` had a value of 0.0, which is likely invalid since ratings typically range from 1-5. I replaced these 0.0 values with NaN to exclude them from analysis.

**3: Convert the nutrition column to list**

The nutrition column contained string representations of lists. I converted these strings to actual Python lists to access individual nutritional components.

**4: Split values in the nutrition column to individual columns of floats**

From the nutrition lists, I extracted each nutritional component and created separate columns for calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. This allowed for more detailed analysis of how specific nutritional factors relate to ratings.

**2: Check dataframe info and types**

Here are all the columns of the cleaned df.
| Column | Type |
|--------|-------------|
| 'name' | object |
| 'id' | int64 |
| 'minutes' | int64 |
| 'contributor_id' | int64 |
| 'submitted' | object |
| 'tags' | object |
| 'nutrition' | object |
| 'n_steps' | int64 |
| 'steps' | object |
| 'description' | object |
| 'ingredients' | object |
| 'n_ingredients' | int64 |
| 'avg_rating' | float64 |
| 'calories' | float64 |
| 'total fat' | float64 |
| 'sugar' | float64 |
| 'sodium' | float64 |
| 'protein ' | float64 |
| 'saturated fat  ' | float64 |
| 'carbohydrates' | float64 |





### Univariate Analysis

I examined the distributions of key variables individually to understand their ranges, central tendencies, and patterns.

**Distribution of Average Rating**

[INSERT VISUALIZATION: histogram of average ratings]

*Explain the distribution here—is it skewed? What's the typical rating? Are there many low-rated recipes?*

**Distribution of Cooking Time**

[INSERT VISUALIZATION: histogram of cooking time in minutes]

*Explain the distribution here—what's the typical cooking time? Are there outliers with extremely long preparation times? How might you handle these outliers?*

**Distribution of Calories**

[INSERT VISUALIZATION: histogram of calorie counts]

*Explain the distribution here—what's the calorie range for most recipes? Are there recipes with unusually high or low calories?*

### Bivariate Analysis

I explored relationships between pairs of variables to identify potential patterns and correlations.

**Relationship Between Calories and Average Rating**

[INSERT VISUALIZATION: scatter plot or box plot showing calories vs average rating]

*Describe what you observe—do higher calorie recipes tend to get higher ratings? Is there a threshold effect?*

**Relationship Between Number of Ingredients and Average Rating**

[INSERT VISUALIZATION: scatter plot or box plot showing n_ingredients vs average rating]

*Describe what you observe—do simpler recipes with fewer ingredients rate better, or do more complex recipes with many ingredients earn higher scores?*

**Relationship Between Sugar Content and Average Rating**

[INSERT VISUALIZATION: scatter plot or box plot showing sugar content vs average rating]

*Describe what you observe—is there a relationship between sugar and ratings? Do sugary recipes get penalized or rewarded by users?*

**Pivot Table: Relationship Between Calories and Average Rating**

[INSERT VISUALIZATION: heatmap or grouped bar chart from pivot table]

*Present a pivot table that categorizes recipes by calorie ranges and shows their average ratings. What patterns emerge across different calorie levels?*
## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

