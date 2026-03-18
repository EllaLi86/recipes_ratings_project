# Investigation on the Relationship Between Cooking Time and Recipe Ratings
### Author: Xinyuan(Ella) Li

## Overview
This data science project, conducted at UCSD, focus on exploring the relationship between the cooking time and rating of the given recipe.

## Introduction

Food is a critical part of our daily lives, and in our fast-paced world, time has become one of our most valuable commodities. Home cooks constantly weigh the trade-off between investing more time in preparation for potentially better results and opting for quick recipes that fit their busy schedules. With this tension in mind, my project investigates the relationship between recipe ratings and the time required to prepare them. Specifically, I ask: **Do recipes with longer preparation times receive higher ratings, or do users prefer quicker recipes that deliver flavor efficiently?**

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


To prepare the datasets for investigating the relationship between cooking time and recipe ratings, I performed several cleaning steps to ensure the data was properly formatted and ready for analysis. These steps address issues arising from how the original data was collected and stored on Food.com.

**1. Calculate average ratings and merge dataframes**

The `interactions` dataset contains multiple ratings from different users for the same recipe, as users on Food.com can rate recipes they've tried. To understand overall recipe quality, I first calculated the average rating for each recipe using the `rating` column from the interactions data. I then merged this aggregated data with the main `recipes` dataframe on `recipe_id` (matched to `id`), creating a new `avg_rating` column. This merge combines recipe characteristics—most importantly cooking time (`minutes`)—with their collective user feedback, enabling analysis of how preparation time relates to recipe popularity.

**2. Replace rating 0.0 with NaN**

During inspection of the `avg_rating` column, I discovered values of 0.0. Food.com's rating system typically uses a 1-5 star scale, where 1 star is the lowest possible rating. A value of 0.0 likely represents missing data or recipes with no ratings rather than genuine user feedback. These zeros would artificially deflate average ratings and skew my analysis of how cooking time affects ratings. I replaced all 0.0 values with NaN to exclude them from calculations, ensuring that only recipes with actual user ratings contribute to my findings.

**3. Convert the nutrition column from string to list**

The `nutrition` column in the raw dataset was stored as a string representation of a list (e.g., `"[138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]"`). This format likely resulted from how the data was exported for the research paper. While my primary focus is on cooking time, I extracted these nutritional components to use as potential confounding variables—for example, longer cooking times might correlate with higher calorie recipes, which could independently influence ratings.

**4. Split nutrition list into individual columns**

Once converted to lists, I extracted each nutritional component and created separate columns. Based on the dataset documentation, the nutrition list follows this order:
- Position 0: calories (#)
- Position 1: total fat (PDV)
- Position 2: sugar (PDV)
- Position 3: sodium (PDV)
- Position 4: protein (PDV)
- Position 5: saturated fat (PDV)
- Position 6: carbohydrates (PDV)

I created seven new float columns: `calories`, `total fat`, `sugar`, `sodium`, `protein`, `saturated fat`, and `carbohydrates`. These allow me to control for nutritional factors when examining the pure relationship between cooking time and ratings.

**5. Convert `submitted` column to datetime**

The `submitted` column, which records when each recipe was posted to Food.com, was initially stored as object/string type. I converted this column to datetime format using `pd.to_datetime()` to enable potential analysis of whether preferences for cooking time have shifted over the 2008-2018 timeframe—for instance, whether busy modern lifestyles have made users prefer quicker recipes in recent years.

**6. Examine cooking time distribution and handle outliers**

Since cooking time (`minutes`) is the central variable in my analysis, I examined its distribution. The column contains some extreme outliers (e.g., recipes claiming to take thousands of minutes) that likely represent data entry errors. For my main analysis, I will consider filtering to reasonable cooking times, but I preserved all values in the cleaned dataframe for transparency.

**7. Verify final dataframe structure**

After completing all cleaning steps, I examined the final dataframe to confirm successful transformations:

| Column | Type |
|--------|------|
| 'name' | object |
| 'id' | int64 |
| 'minutes' | int64 |
| 'contributor_id' | int64 |
| 'submitted' | datetime64[ns] |
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
| 'protein' | float64 |
| 'saturated fat' | float64 |
| 'carbohydrates' | float64 |

The cleaned `merged` dataframe contains **83,782 rows** and **20 columns**. Below are the first 5 rows, highlighting the most relevant columns for my investigation of cooking time and recipe ratings. Scroll right to view all columns.

| name | id | minutes | contributor_id | submitted | n_steps | n_ingredients | avg_rating | calories | total fat | sugar | sodium | protein | saturated fat | carbohydrates |
|------|-----|---------|----------------|-----------|---------|---------------|------------|----------|-----------|-------|--------|---------|----------------|---------------|
| 1 brownies in the world best ever | 333281 | 40 | 985201 | 2008-10-27 | 10 | 9 | 4.0 | 138.4 | 10 | 50 | 3 | 3 | 19 | 6 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 2011-04-11 | 12 | 11 | 5.0 | 595.1 | 46 | 211 | 22 | 13 | 51 | 26 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 2008-05-30 | 6 | 9 | 5.0 | 194.8 | 20 | 6 | 32 | 22 | 36 | 3 |
| millionaire pound cake | 286009 | 120 | 461724 | 2008-02-12 | 7 | 7 | 5.0 | 878.3 | 63 | 326 | 13 | 20 | 123 | 39 |
| 2000 meatloaf | 475785 | 90 | 2202916 | 2012-03-06 | 17 | 13 | 5.0 | 267.0 | 30 | 12 | 12 | 29 | 48 | 2 |


### Univariate Analysis

I examined the distributions of key variables individually to understand their ranges, central tendencies, and patterns.

#### Distribution of Average Rating
The distribution of average ratings is heavily left-skewed, with the vast majority of recipes receiving ratings between 4.5 and 5 stars. This indicates that users tend to rate recipes positively overall, with 44,076 recipes falling in the highest rating bin, making it challenging to distinguish between truly exceptional recipes and merely good ones.

<iframe
  src="assets/avg_rating_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


#### Distribution of Cooking Time

Since approximately 12.3% of recipes have cooking times exceeding 100 minutes (which could skew the visualization), I filtered to show only recipes with cooking times under 100 minutes for a clearer view of the typical preparation time range.

The box plot reveals that the median cooking time is around 35 minutes, with the interquartile range (IQR) spanning from approximately 20 to 50 minutes. The presence of numerous outliers beyond the upper whisker indicates that while most recipes are relatively quick to prepare, there exists a substantial number of more time-intensive recipes that could appeal to users looking for weekend projects or special occasion meals.

<iframe
  src="assets/cooking_time_boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Distribution of Calories

The calorie distribution is right-skewed, with most recipes clustering in the lower to moderate calorie ranges. This suggests that the platform caters primarily to everyday cooking needs, though the long tail of higher-calorie recipes likely represents indulgent desserts and rich main courses that users prepare for special occasions.
<iframe
  src="assets/calories_histogram.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates: Percentage of Recipes by Calorie Group and Rating Category

This pivot table displays the percentage distribution of ratings across different calorie groups. Each row represents a calorie range, and each column shows what proportion of recipes in that calorie range received ratings in each category. The percentages in each row sum to 100%.

| Calorie Group | Very Poor (0-2) | Poor (2-3) | Fair  (3-3.5) | Good (3.5-4) | Very Good (4-4.5) | Excellent (4.5-5) |
|---------------|-----------------|------------|--------------|--------------|-------------------|-------------------|
| <200 | 2.34% | 5.52% | 2.70% | 16.23% | 9.45% | 63.76% |
| 200-400 | 2.26% | 5.67% | 2.65% | 17.03% | 9.98% | 62.41% |
| 400-600 | 2.35% | 5.86% | 2.40% | 17.85% | 9.86% | 61.68% |
| 600-800 | 2.18% | 6.35% | 2.72% | 17.94% | 9.50% | 61.32% |
| 800-1000 | 2.34% | 6.49% | 2.87% | 16.61% | 8.83% | 62.85% |
| 1000-1500 | 2.86% | 6.29% | 3.31% | 17.51% | 9.23% | 60.80% |
| 1500-2000 | 3.27% | 6.42% | 3.87% | 18.52% | 8.47% | 59.44% |



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

