Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods? This project by Beomsuk Seo and Ethan Lin for DSC 80 at UCSD aims to answer that.

# Introduction

One third of American adults are overweight, and another third are obese. This physical health crisis has many causes, each of which must be addressed accordingly. One such cause is the American obsession with comfort food. While satisfying and filling, foods like burgers, mac and cheese, and barbecue are notoriously unhealthy, contributing to the rampant obesity across the country. To analyze a potential instance of trend, we will look at a dataset of Food.com recipes (RAW_recipes.csv) and a dataset of Food.com reviews (RAW_interactions.csv). 

These two datasets were originally scraped by the authors of a recommender systems paper. They consisted of 83,782 and 731,927 rows respectively. The data captured in these datasets record a decade's worth of user-submitted recipes and reviews of those recipes. They present a large sample of the many home-cooked meals being prepared across America, as well as the feedback to the recipes they were cooked from. From this, we can analyze if American home-cooking is impacted by the general trend of American obsession with unhealthy foods.

The columns that we were interested in are as follows:

'RAW_recipes.csv'

| Column | Description |
| --- |:--- |
| 'name' | Recipe name |
| 'id' | Recipe ID |
| 'ingredients' | List of all ingredients used in the recipe, sorted by proportions
| 'nutrition' | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| 'n_steps' | Number of steps in recipe |

'RAW_interactions.csv'

| Column | Description |
| --- |:--- |
|'recipe_id' | Recipe ID |
|'rating' | Rating given |

We then merged these two datasets, producing a dataset with 234,429 rows. In addition, for each row, we included an 'avg_rating' column that lists the average rating given to that recipe by reviewers.

With these two datasets, the question we aim to answer is: *Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods?*



# Cleaning and EDA

After we dropped the columns we weren't using and converting the 'ingredient' and 'nutrition' columns from strings into lists of strings, we replaced the 'nutrition' column with columns for each of the nutritional contents listed. Then, using this nutritional info, we calculated a nutrition score for each recipe.

![alt text](https://www.fda.gov/files/nfl-howtounderstand-labeled.png)

To produce this nutrition score, we used the basis of measuring nutrition content supplied by the FDA, wherein 5% or less is considered low and 20% or more is considered high. We only considered Fat, Saturated Fat, Sodium, and Sugar, as they play the largest role in the healthiness ofa recipe. Since each nutritional content was already listed in % Daily Value, we then assigned points for each nutritional content's %DV:

- 0 if 20% or more
- 1 if 5% - 20%
- 2 if 5% or less

With that, we added a new column named 'score', which indicated the nutrition score of each recipe in the dataset. 

Some values for the nutritional content columns were far too extreme to be fit for human consumption 
Our cleaned dataset looks like this:

| name                                 |     id | ingredients                                                                                                                                                                    |   rating |   avg_rating |   n_steps |   Total Fat |   Saturated Fat |   Sugar |   Sodium |   Score |
|:-------------------------------------|-------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|-------------:|----------:|------------:|----------------:|--------:|---------:|--------:|
| 1 brownies in the world    best ever | 333281 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |        4 |            4 |        10 |          10 |              19 |      50 |        3 |       4 |
| 1 in canada chocolate chip cookies   | 453467 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |        5 |            5 |        12 |          46 |              51 |     211 |       22 |       0 |
| 412 broccoli casserole               | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |
| 412 broccoli casserole               | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |
| 412 broccoli casserole               | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |

We also generated another dataframe that only takes recipes with a 'score' of either 0-2 or 6-8, which had an additional boolean column labelled 'Healthy', with scores of 0-2 being False and 6-8 being True.

name                               |     id | ingredients                                                                                                                                                           |   rating |   avg_rating |   n_steps |   Total Fat |   Saturated Fat |   Sugar |   Sodium |   Score | Healthy   |
|:-----------------------------------|-------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|-------------:|----------:|------------:|----------------:|--------:|---------:|--------:|:----------|
| 1 in canada chocolate chip cookies | 453467 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']           |        5 |            5 |        12 |          46 |              51 |     211 |       22 |       0 | False     |
| 412 broccoli casserole             | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions'] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |
| 412 broccoli casserole             | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions'] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |
| 412 broccoli casserole             | 306168 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions'] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |


# Assessment of Missingness


# Hypothesis Testing