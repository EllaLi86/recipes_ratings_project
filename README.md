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
I calculated the average rating for each recipe to understand overall recipe quality as perceived by users from `interactions` dataframe. Then, I merged two dataframes to combine recipe information with user ratings. 

**2. Replace rating 0.0 with NaN**
Upon inspection, I noticed some `avg_rating` had a value of 0.0, which is likely invalid since ratings typically range from 1-5. I replaced these 0.0 values with NaN to exclude them from analysis.

**3. Convert the nutrition column to list**
The `nutrition` column contained string representations of lists. I converted these strings to actual Python lists to access individual nutritional components.

**4. Split values in the nutrition column to individual columns of floats**
From the nutrition lists, I extracted each nutritional component and created separate columns for calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. This allowed for more detailed analysis of how specific nutritional factors relate to ratings.

**5. Convert `submitted` column datetime**
The `submitted` column is stored as objects. I converted it into datetime to conduct the later analysis on trends over time if it is necessary.

**6. Check final dataframe info and types**
Here are all the columns of the cleaned df. 

| Column | Type |
|--------|-------------|
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
| 'protein ' | float64 |
| 'saturated fat  ' | float64 |
| 'carbohydrates' | float64 |

The `merged` dataframe contains **83,782 rows** and **20 columns**. Here are the first 5 rows.
## Data Cleaning Results

### Complete Recipe Data (First 5 Rows)

| name | id | minutes | contributor_id | submitted | tags | nutrition | n_steps | steps | description | ingredients | n_ingredients | avg_rating | calories | total fat | sugar | sodium | protein | saturated fat | carbohydrates |
|------|-----|---------|----------------|-----------|------|-----------|---------|-------|-------------|-------------|---------------|------------|----------|-----------|-------|--------|---------|----------------|---------------|
| 1 brownies in the world best ever | 333281 | 40 | 985201 | 2008-10-27 | 60-minutes-or-less, time-to-make, course, main-ingredient, preparation, for-large-groups, desserts, lunch, snacks, cookies-and-brownies, chocolate, bar-cookies, brownies, number-of-servings | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] | 10 | heat the oven to 350f and arrange the rack in the middle, line an 8-by-8-inch glass baking dish with aluminum foil, combine chocolate and butter in a medium saucepan and cook over medium-low heat, stirring frequently, until evenly melted, remove from heat and let cool to room temperature, combine eggs, sugar, cocoa powder, vanilla extract, espresso, and salt in a large bowl and briefly stir until just evenly incorporated, add cooled chocolate and mix until uniform in color, add flour and stir until just incorporated, transfer batter to the prepared baking dish, bake until a tester inserted in the center of the brownies comes out clean, about 25 to 30 minutes, remove from the oven and cool completely before cutting | these are the most chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make... seriously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain... either way they're pure heaven! | bittersweet chocolate, unsalted butter, eggs, granulated sugar, unsweetened cocoa powder, vanilla extract, brewed espresso, kosher salt, all-purpose flour | 9 | 4.0 | 138.4 | 10 | 50 | 3 | 3 | 19 | 6 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 2011-04-11 | 60-minutes-or-less, time-to-make, cuisine, preparation, north-american, for-large-groups, canadian, british-columbian, number-of-servings | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] | 12 | pre-heat oven the 350 degrees f, in a mixing bowl, sift together the flours and baking powder, set aside, in another mixing bowl, blend together the sugars, margarine, and salt until light and fluffy, add the eggs, water, and vanilla to the margarine/sugar mixture and mix together until well combined, add in the flour mixture to the wet ingredients and blend until combined, scrape down the sides of the bowl and add the chocolate chips, mix until combined, scrape down the sides to the bowl again, using an ice cream scoop, scoop evenly rounded balls of dough and place of cookie sheet about 1-2 inches apart to allow for spreading during baking, bake for 10-15 minutes or until golden brown on the outside and soft & chewy in the center, serve hot and enjoy! | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead. | white sugar, brown sugar, salt, margarine, eggs, vanilla, water, all-purpose flour, whole wheat flour, baking soda, chocolate chips | 11 | 5.0 | 595.1 | 46 | 211 | 22 | 13 | 51 | 26 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 2008-05-30 | 60-minutes-or-less, time-to-make, course, main-ingredient, preparation, side-dishes, vegetables, easy, beginner-cook, broccoli | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0] | 6 | preheat oven to 350 degrees, spray a 2 quart baking dish with cooking spray, set aside, in a large bowl mix together broccoli, soup, one cup of cheese, garlic powder, pepper, salt, milk, 1 cup of french onions, and soy sauce, pour into baking dish, sprinkle remaining cheese over top, bake for 25 minutes or until cheese is lightly browned, sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly, about 10 more minutes | since there are already 411 recipes for broccoli casserole posted to "zaar", i decided to call this one #412 broccoli casserole. i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup. submitted to "zaar" on may 28th, 2008 | frozen broccoli cuts, cream of chicken soup, sharp cheddar cheese, garlic powder, ground black pepper, salt, milk, soy sauce, french-fried onions | 9 | 5.0 | 194.8 | 20 | 6 | 32 | 22 | 36 | 3 |
| millionaire pound cake | 286009 | 120 | 461724 | 2008-02-12 | time-to-make, course, cuisine, preparation, occasion, north-american, desserts, american, southern-united-states, dinner-party, holiday-event, cakes, dietary, christmas, thanksgiving, low-sodium, low-in-something, taste-mood, sweet, 4-hours-or-less | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] | 7 | preheat the oven to 300 degrees, grease a 10-inch tube pan with butter, dust the bottom and sides with flour, and set aside, in a large mixing bowl, cream the butter and sugar with an electric mixer and add the eggs one at a time, beating after each addition, alternately add the flour and milk, stirring till the batter is smooth, add the two extracts and stir till well blended, scrape the batter into the prepared pan and bake till a cake tester or knife blade inserted in the center comes out clean, about 1 1/2 hours, cool the cake in the pan on a rack for 5 minutes, then turn it out on the rack to cool completely | why a millionaire pound cake? because it's super rich! this scrumptious cake is the pride of an elderly belle from jackson, mississippi. the recipe comes from "the glory of southern cooking" by james villas. | butter, sugar, eggs, all-purpose flour, whole milk, pure vanilla extract, almond extract | 7 | 5.0 | 878.3 | 63 | 326 | 13 | 20 | 123 | 39 |
| 2000 meatloaf | 475785 | 90 | 2202916 | 2012-03-06 | time-to-make, course, main-ingredient, preparation, main-dish, potatoes, vegetables, 4-hours-or-less, meatloaf, simply-potatoes2 | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0] | 17 | pan fry bacon, and set aside on a paper towel to absorb excess grease, mince yellow onion, red bell pepper, and add to your mixing bowl, chop garlic and set aside, put 1 tbsp olive oil into a saut pan, along with chopped garlic, teaspoons white pepper and a pinch of kosher salt, bring to a medium heat to sweat your garlic, preheat oven to 350f, coarsely chop your baby spinach add to your heated pan, stir frequently for approximately 5 min to wilt, add your spinach to the mixing bowl, chop your now cooled bacon, and add it to the mixing bowl, add your meatloaf mix to the bowl, with one egg and mix till thoroughly combined, add your goat cheese, one egg, 1/8 tsp white pepper and 1/8 tsp of kosher salt and mix till thoroughly combined, transfer to a 9x5 meatloaf pan, and cook for 60 min or until the internal temperature is at least 160f, let stand for 5min, melt 1 tbsp unsalted butter into a frying pan, and cook up to three eggs at a time, crack each egg into a separate dish, in order to prevent egg shells from reaching the pan, then add salt and pepper to taste, wait until the egg whites are firm looking, but slightly runny on top before flipping your eggs, after flipping, wait 10-20 seconds before removing each egg and placing it over your slices of meatloaf | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese. | meatloaf mixture, unsmoked bacon, goat cheese, unsalted butter, eggs, baby spinach, yellow onion, red bell pepper, simply potatoes shredded hash browns, fresh garlic, kosher salt, white pepper, olive oil | 13 | 5.0 | 267.0 | 30 | 12 | 12 | 29 | 48 | 2 |




### Univariate Analysis

I examined the distributions of key variables individually to understand their ranges, central tendencies, and patterns.

**Distribution of Average Rating**

<iframe
  src="assets/distribution_of_avg_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


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

