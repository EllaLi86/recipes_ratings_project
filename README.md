# Investigation on the Relationship Between Cooking Time and Recipe Ratings
### Author: Xinyuan(Ella) Li

## Overview
This data science project, conducted at UCSD, focus on exploring the relationship between the cooking time and rating of the given recipe.

## Introduction

Food is a critical part of our daily lives, and in our fast-paced world, time has become one of our most valuable commodities. Home cooks constantly weigh the trade-off between investing more time in preparation for potentially better results and opting for quick recipes that fit their busy schedules. With this tension in mind, my project investigates the relationship between recipe ratings and the time required to prepare them. Specifically, I ask: **Do recipes with longer preparation times(> 60 minutes) receive higher ratings, or do users prefer quicker recipes(<= 30 minutes) that deliver flavor efficiently?**

To explore this question, I analyze two datasets from [food.com](https://www.food.com/recipe/chickpea-and-fresh-tomato-toss-51631), which contain recipe information and user interactions since 2008.

### Dataset 1: Recipes (`raw_recipes`)  
- **Rows:** 83,782 (unique recipes)  
- **Columns:** 12  

| Column | Description |
|--------|-------------|
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Time required to prepare the recipe |
| `contributor_id` | ID of the user who submitted the recipe |
| `submitted` | Date the recipe was submitted |
| `tags` | Tags associated with the recipe |
| `nutrition` | Nutrition info `[calories, total fat, sugar, sodium, protein, saturated fat, carbohydrates]` |
| `n_steps` | Number of steps in the recipe |
| `steps` | Step-by-step instructions |
| `description` | User-provided description |
| `ingredients` | Ingredients list |
| `n_ingredients` | Number of ingredients |

### Dataset 2: User Interactions (`interactions`)  
- **Rows:** 731,927 (reviews)  
- **Columns:** 5  

| Column | Description |
|--------|-------------|
| `user_id` | ID of the user providing the review |
| `recipe_id` | Recipe being reviewed |
| `date` | Date of interaction |
| `rating` | Rating given (1–5 scale) |
| `review` | Review text |

### Focus of Analysis  
To answer my research question, I focus on the following columns:  
- **`minutes`** – Recipe preparation and cooking time (from `raw_recipes`)  
- **`rating`** – Individual user ratings (from `interactions`)  
- **`average_rating`** – Mean rating per recipe (calculated from `interactions`)  

**Given the datasets, I am investigating whether there is a meaningful relationship between cooking time and how users rate recipes**. Understanding the relationship between cooking time and user ratings provides insight into what people value in recipes. These insights can help contributors tailor recipes to match user preferences—whether that means creating elaborate, time-intensive dishes or optimizing recipes for efficiency. Furthermore, these findings could inform recommendation systems that better align with users’ time constraints and cooking habits, making meal planning easier for busy home cooks.


## Data Cleaning and Exploratory Data Analysis

To prepare the datasets for analyzing the relationship between cooking time and recipe ratings, I performed several data cleaning steps. These steps address inconsistencies and structural issues that arise from how data is generated on Food.com—where users independently submit recipes and provide ratings, leading to duplication, missing values, and formatting challenges.

### 1. Aggregate ratings and merge datasets

The `interactions` dataset contains multiple ratings per recipe, since different users can review the same recipe. Because my analysis focuses on overall recipe quality, I aggregated the `rating` column by `recipe_id` to compute an average rating for each recipe.

I then merged this aggregated table with the `raw_recipes` dataset by matching `recipe_id` (from `interactions`) with `id` (from `raw_recipes`). This produced a unified dataframe containing both recipe attributes (e.g., `minutes`) and user feedback (`avg_rating`). This step is essential because the original datasets separate recipe metadata from user evaluations.

### 2. Handle invalid ratings (0.0 → NaN)

While inspecting the `avg_rating` column, I observed values equal to 0.0. Since Food.com uses a 1–5 rating scale, a value of 0.0 does not represent a valid rating and likely indicates missing data or recipes with no reviews.

To prevent these values from biasing the analysis, I replaced all 0.0 entries with `NaN`. This ensures that only recipes with actual user feedback are included when analyzing the relationship between cooking time and ratings.

### 3. Convert `nutrition` from string to list

The `nutrition` column was originally stored as a string representation of a list (e.g., `"[138.4, 10.0, 50.0, ...]"`). This format likely resulted from how the dataset was exported.

I converted these strings into actual Python lists so that the individual nutritional components could be accessed programmatically. Although nutrition is not my primary variable of interest, it may act as a confounder (e.g., more complex or higher-calorie recipes might take longer and also receive different ratings).

### 4. Extract nutrition into separate columns

After converting the `nutrition` column, I split it into seven separate numeric columns based on the documented order:

- `calories`
- `total fat`
- `sugar`
- `sodium`
- `protein`
- `saturated fat`
- `carbohydrates`

This transformation improves interpretability and enables potential future analysis controlling for nutritional factors.

### 5. Convert `submitted` to datetime

The `submitted` column was initially stored as a string. I converted it to a datetime format using `pd.to_datetime()`.

This allows for potential temporal analysis, such as examining whether user preferences for cooking time have changed over time—an important consideration given evolving lifestyles and cooking habits.

### 6. Final dataset verification

After cleaning, I verified the structure and data types of the final merged dataframe:

| Column | Type |
|--------|------|
| `name` | object |
| `id` | int64 |
| `minutes` | int64 |
| `contributor_id` | int64 |
| `submitted` | datetime64[ns] |
| `tags` | object |
| `nutrition` | object |
| `n_steps` | int64 |
| `steps` | object |
| `description` | object |
| `ingredients` | object |
| `n_ingredients` | int64 |
| `avg_rating` | float64 |
| `calories` | float64 |
| `total fat` | float64 |
| `sugar` | float64 |
| `sodium` | float64 |
| `protein` | float64 |
| `saturated fat` | float64 |
| `carbohydrates` | float64 |

The cleaned `merged` dataframe contains **83,782 rows** and **20 columns**.

### Preview of cleaned data

Below are the first five rows of the cleaned dataset, showing the most relevant variables for this analysis:

| name | id | minutes | contributor_id | submitted | n_steps | n_ingredients | avg_rating | calories | total fat | sugar | sodium | protein | saturated fat | carbohydrates |
|------|-----|---------|----------------|-----------|---------|---------------|------------|----------|-----------|-------|--------|---------|----------------|---------------|
| 1 brownies in the world best ever | 333281 | 40 | 985201 | 2008-10-27 | 10 | 9 | 4.0 | 138.4 | 10 | 50 | 3 | 3 | 19 | 6 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 2011-04-11 | 12 | 11 | 5.0 | 595.1 | 46 | 211 | 22 | 13 | 51 | 26 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 2008-05-30 | 6 | 9 | 5.0 | 194.8 | 20 | 6 | 32 | 22 | 36 | 3 |
| millionaire pound cake | 286009 | 120 | 461724 | 2008-02-12 | 7 | 7 | 5.0 | 878.3 | 63 | 326 | 13 | 20 | 123 | 39 |
| 2000 meatloaf | 475785 | 90 | 2202916 | 2012-03-06 | 17 | 13 | 5.0 | 267.0 | 30 | 12 | 12 | 29 | 48 | 2 |

### Univariate Analysis

I examined the distributions of key variables individually to better understand their ranges, central tendencies, and overall patterns before exploring relationships between variables.

#### Distribution of Average Rating

The distribution of average ratings is heavily left-skewed, with the vast majority of recipes receiving ratings between 4.5 and 5 stars. This suggests that users on Food.com tend to rate recipes very positively, which may reduce variability and make it more difficult to distinguish between moderately good and truly exceptional recipes.

<iframe
  src="assets/avg_rating_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Distribution of Cooking Time

This histogram  below shows the frequency distribution of recipe cooking times. The plot reveals a strong right-skewed distribution, with the vast majority of recipes having cooking times under 200 minutes, and a steep drop-off in frequency as cooking time increases beyond that point.
<iframe
  src="assets/cooking_time_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Because approximately 6% of recipes have cooking times exceeding 200 minutes (which can skew visualization), I restricted this plot to recipes under 200 minutes to better capture typical cooking behavior.

The box plot shows a median cooking time of about 35 minutes, with an interquartile range from roughly 20 to 58 minutes. This indicates that most recipes are relatively quick to prepare, though the presence of many high-end outliers suggests a meaningful subset of more time-intensive recipes.
<iframe
  src="assets/cooking_time_boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

To directly address my research question—whether cooking time influences how users rate recipes—I examine the relationship between cooking time (`minutes`) and average recipe rating (`avg_rating`). By comparing groups of recipes with different preparation times, I can assess whether longer, potentially more complex recipes tend to receive higher ratings, or if quicker recipes perform just as well in terms of user satisfaction.

#### Average Ratings: Quick vs. Regular Recipes

This side-by-side box plot compares average ratings between quick recipes (cooking time ≤ 35 minutes) and regular recipes (cooking time > 35 minutes). Both groups have similar distribution, indicating that cooking time alone does not strongly differentiate recipe quality.
<iframe
  src="assets/minutes_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates: Percentage of Recipes by Calorie Group and Rating Category

### Interesting Aggregates: Percentage of Recipes by Calorie Group and Rating Category

To further explore factors that may influence recipe ratings, I constructed a pivot table showing the percentage distribution of rating categories within each calorie group. Each row represents a calorie range, and each column indicates the proportion of recipes in that group that fall into a specific rating category. Percentages within each row sum to 100%.

| Calorie Group | Very Poor (0-2) | Poor (2-3) | Fair (3-3.5) | Good (3.5-4) | Very Good (4-4.5) | Excellent (4.5-5) |
|---------------|-----------------|------------|--------------|--------------|-------------------|-------------------|
| <200 | 2.34% | 5.52% | 2.70% | 16.23% | 9.45% | 63.76% |
| 200-400 | 2.26% | 5.67% | 2.65% | 17.03% | 9.98% | 62.41% |
| 400-600 | 2.35% | 5.86% | 2.40% | 17.85% | 9.86% | 61.68% |
| 600-800 | 2.18% | 6.35% | 2.72% | 17.94% | 9.50% | 61.32% |
| 800-1000 | 2.34% | 6.49% | 2.87% | 16.61% | 8.83% | 62.85% |
| 1000-1500 | 2.86% | 6.29% | 3.31% | 17.51% | 9.23% | 60.80% |
| 1500-2000 | 3.27% | 6.42% | 3.87% | 18.52% | 8.47% | 59.44% |

This table shows that across all calorie groups, the majority of recipes fall into the “Excellent” (4.5–5) category, reinforcing the earlier observation that ratings are generally high. While there is a slight trend where higher-calorie recipes have a marginally lower proportion of “Excellent” ratings and a slightly higher proportion of lower rating categories, these differences are relatively small.

Overall, this suggests that calorie content does not strongly influence recipe ratings and is unlikely to be a major confounding variable in the relationship between cooking time and ratings.

<iframe
  src="assets/calories_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Assessment of Missingness

Three columns in the merged dataset have missing values: 'name', 'description', and 'avg_rating'. 

### NMAR Analysis
I believe that the 70 missingness of the **'description'** column is **NMAR** (Not Missing at Random). If people feel indifferent about a recipe, they are less likely to leave a review since they would feel they have nothing significant to say. People usually leave a review only when they have stronger emotions toward the recipe—whether positive or negative. Their emotions motivate them to navigate to the page, click through multiple buttons, and take time out of their day to write. For example, someone who thoroughly enjoyed a recipe would be willing to do all the work to leave a positive review, while someone who had a terrible experience might be motivated to warn others. Those with neutral experiences, however, have no such motivation, making their reviews systematically missing.

### Missingness Dependency
Now I examine the missingness of `avg_rating` in the merged DataFrame by testing the dependency of its missingness.

**Cooking Time and Rating Missingness**

I am investigating whether the missingness in the `rating` column depends on the column `minutes`, which is the cooking time of the recipe.

**Null Hypothesis:** The missingness of ratings does not depend on the cooking time (minutes) of the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the cooking time (minutes) of the recipe.

**Test Statistic:** The absolute difference in mean cooking time between the group with missing ratings and the group without missing ratings.

**Significance Level:** 0.05
<iframe
  src="assets/rating_missing_time_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/rating_missing_time_permutation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Results:** 
- Observed Difference: 117.34 minutes
- P-value: 0.041

Since the p-value (0.041) is less than our significance level of 0.05, we **reject the null hypothesis**. This suggests that the missingness of ratings does depend on the cooking time of the recipe.

**Protein and Rating Missingness**

I also investigated whether the missingness of ratings depends on the protein content of recipes. 

**Null Hypothesis:** The missingness of ratings does not depend on the protein amount of the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the protein amount of the recipe.

**Test Statistic:** The absolute difference in mean protein between the group with missing ratings and the group without missing ratings.

**Significance Level:** 0.05
<iframe
  src="assets/rating_missing_protein_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/rating_missing_protein_permutation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Results:** 
- Observed Difference: 1.2873110276228346
- P-value: 0.183

The permutation test yielded a p-value of 0.183, which is greater than our significance level of 0.05. Therefore, we fail to reject the null hypothesis.

This result provides insufficient statistical evidence to conclude that the missingness of ratings depends on the protein content of recipes. The observed difference in mean protein content between recipes with missing ratings and those with complete ratings could reasonably occur by random chance.


## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis

