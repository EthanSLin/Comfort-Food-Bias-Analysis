Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods? This project by Beomsuk Seo and Ethan Lin for DSC 80 at UCSD aims to answer that.

# Introduction

One third of American adults are overweight, and another third are obese. This physical health crisis has many causes, each of which must be addressed accordingly. One such cause is the American obsession with comfort food. While satisfying and filling, foods like burgers, mac and cheese, and barbecue are notoriously unhealthy, contributing to the rampant obesity across the country. To analyze a potential instance of trend, we will look at a dataset of Food.com recipes (RAW_recipes.csv) and a dataset of Food.com reviews (RAW_interactions.csv). 

These two datasets were originally scraped by the authors of a recommender systems paper. They consisted of 83,782 and 731,927 rows respectively. The data captured in these datasets record a decade`s worth of user-submitted recipes and reviews of those recipes. They present a large sample of the many home-cooked meals being prepared across America, as well as the feedback to the recipes they were cooked from. From this, we can analyze if American home-cooking is impacted by the general trend of American obsession with unhealthy foods.

The columns that we were interested in are as follows:

`RAW_recipes.csv`

| Column | Description |
| --- |:--- |
| `name` | Recipe name |
| `id` | Recipe ID |
| `ingredients` | List of all ingredients used in the recipe, sorted by proportions
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `n_steps` | Number of steps in recipe |

`RAW_interactions.csv`

| Column | Description |
| --- |:--- |
|`recipe_id` | Recipe ID |
|`rating` | Rating given |

We then merged these two datasets, producing a dataset with 234,429 rows. In addition, for each row, we included an `avg_rating` column that lists the average rating given to that recipe by reviewers.

With these two datasets, the question we aim to answer is: *Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods?*



# Cleaning and EDA

## Data Cleaning and Organizing
After we dropped the columns we weren't using and converting the `ingredient` and `nutrition` columns from strings into lists of strings, we replaced the `nutrition` column with columns for each of the nutritional contents listed. Then, using this nutritional info, we calculated a nutrition score for each recipe.

![alt text](https://www.fda.gov/files/nfl-howtounderstand-labeled.png)

To produce this nutrition score, we used the basis of measuring nutrition content supplied by the FDA, wherein 5% or less is considered low and 20% or more is considered high. We only considered Fat, Saturated Fat, Sodium, and Sugar, as they play the largest role in the healthiness ofa recipe. Since each nutritional content was already listed in % Daily Value, we then assigned points for each nutritional content`s %DV:

- 0 if 20% or more
- 1 if 5% - 20%
- 2 if 5% or less

With that, we added a new column named `score`, which indicated the nutrition score (between 0 and 8) of each recipe in the dataset. 

Some values for the nutritional content columns were far too extreme to be fit for human consumption 
Our cleaned dataset looks like this:

| name                                 |     id | ingredients                                                                                                                                                                    |   rating |   avg_rating |   n_steps |   Total Fat |   Saturated Fat |   Sugar |   Sodium |   Score |
|:-------------------------------------|-------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|-------------:|----------:|------------:|----------------:|--------:|---------:|--------:|
| 1 brownies in the world    best ever | 333281 | [`bittersweet chocolate`, `unsalted butter`, `eggs`, `granulated sugar`, `unsweetened cocoa powder`, `vanilla extract`, `brewed espresso`, `kosher salt`, `all-purpose flour`] |        4 |            4 |        10 |          10 |              19 |      50 |        3 |       4 |
| 1 in canada chocolate chip cookies   | 453467 | [`white sugar`, `brown sugar`, `salt`, `margarine`, `eggs`, `vanilla`, `water`, `all-purpose flour`, `whole wheat flour`, `baking soda`, `chocolate chips`]                    |        5 |            5 |        12 |          46 |              51 |     211 |       22 |       0 |
| 412 broccoli casserole               | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`]          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |
| 412 broccoli casserole               | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`]          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |
| 412 broccoli casserole               | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`]          |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 |

We also generated an alternate dataframe that only takes recipes with a `score` of either 0-2 or 6-8, which had an additional boolean column labelled `Healthy`, with scores of 0-2 being False and 6-8 being True.

This dataframe is used when we want to focus on the two extremes of the nutrition scores.

name                               |     id | ingredients                                                                                                                                                           |   rating |   avg_rating |   n_steps |   Total Fat |   Saturated Fat |   Sugar |   Sodium |   Score | Healthy   |
|:-----------------------------------|-------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------:|-------------:|----------:|------------:|----------------:|--------:|---------:|--------:|:----------|
| 1 in canada chocolate chip cookies | 453467 | [`white sugar`, `brown sugar`, `salt`, `margarine`, `eggs`, `vanilla`, `water`, `all-purpose flour`, `whole wheat flour`, `baking soda`, `chocolate chips`]           |        5 |            5 |        12 |          46 |              51 |     211 |       22 |       0 | False     |
| 412 broccoli casserole             | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |
| 412 broccoli casserole             | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |
| 412 broccoli casserole             | 306168 | [`frozen broccoli cuts`, `cream of chicken soup`, `sharp cheddar cheese`, `garlic powder`, `ground black pepper`, `salt`, `milk`, `soy sauce`, `french-fried onions`] |        5 |            5 |         6 |          20 |              36 |       6 |       32 |       1 | False     |

## Univariate Analysis

<iframe src="assets/univariate.html" width=800 height=600 frameBorder=0></iframe>

When we plot the distribution of scores across all our recipes, it becomes apparent that the scores are quite skewed, with most recipes having rather low nutrition scores. The median of the recipe's score is around 2-3 and the distribution being positively skewed on the right side, with a notable, slight hump at score 6.

<iframe src="assets/Red_Dist_of_Avg_Rating.html" width=800 height=600 frameBorder=0></iframe>

We also noticed that there is an extreme negative skew when in terms of ratings. The distribution of ratings is so heavily skewed that ratings less than 3.6 stars are considered outliers, which indicates just how concentrated ratings are.

## Bivariate Analysis

<iframe src="assets/Score_Distribution_by_Average_Rating.html" width=800 height=600 frameBorder=0></iframe>

Due to the extremely dense concentration of high `avg_rating` values, we note that there is not much difference between the distributions of the `avg_rating` between recipes that we labelled as `Healthy` and the recipes that we labelled as `Unhealthy`. In fact, `Healthy` recipes appear to have very slightly `avg_rating` values, which actually goes against our hypothesis that unhealthy foods have higher average ratings.

## Interesting Aggregates

| Healthy   |   avg_rating |
|:----------|-------------:|
| False     |      4.6768  |
| True      |      4.68772 |

Once again, by creating a pivot table around the `Healthy` and `avg_rating` columns, we note that the mean of healthy recipe's `avg_rating` values are slightly higher than the mean of unhealthy recipe`s `avg_rating` values, providing evidence that goes against our theory

# Assessment of Missingness

## NMAR Analaysis

We believe that the `rating` column is NMAR. People are most likely to rate recipes that they have strong feelings for it. So while they might have reviewed the recipe, there is a strong chance they did not leave a rating if they didn't feel strongly enough to do so.

If it were possible to know if the reviewer had actually finished making the recipe, the missingness could be explained by a lack of interest in the recipe, which would make the `rating` column MAR.

## Missingness Dependency

<iframe src="assets/Missing_Dependency.html" width=800 height=600 frameBorder=0></iframe>

While exploring possible missingness dependencies, we compared these two distributions:
- The distribution of `Healthy` when `rating` is missing.
- The distribution of `Healthy` when `rating` is not missing

We then analyzed if `rating` was dependent on the `n_steps` and `avg_rating` columns. To do this, we ran permutation tests at 5% significance levels.

For the permutation test between `rating` and `n_steps`, we got a p-value of 100%, so we failed to reject the null hypothesis. This means we can conclude that missingness in the `rating` column is not dependent on the `n_steps` column.

For the permutation test between `rating` and `avg_rating`, the 95th percentile of the empirical absolute mean differences distribution was 0.00707, and our observed difference in absolute means was 0.063. Since our observed difference was far greater than the 95th percentile, we rejected the null hypothesis. This means we conclude that missingness in the `rating` column is dependent on the `avg_rating` column.

# Hypothesis Testing

### Hypotheses:

- Null: The ratings of healthy and unhealthy foods have the same distribution and the observed differences are due to random chance.
- Alternative: Healthy foods have lower ratings than unhealthy foods on average. The observed differences cannot be explained by random chance alone.

We decided to use the *difference in group means* as our test statistic. 
We *did not use the absolute difference in group means* because the statistic we wanted was directional. We elected to use a significance level of 5%, as this is the standard significance level for hypothesis testing.

<iframe src="assets/Health_Fig_Permutation_Test.html" width=800 height=600 frameBorder=0></iframe>

`np.array(rating_diffs <= observed_rating_diff).mean()`

0.0

P-value ~= 0.0

The red line above represents our observed value of the difference in group means.
Under our null hypothesis, we almost never see a difference as large as our observed value.
Therefore, we conclude to reject the null hypothesis that the ratings of healthy and unhealthy foods have the same distribution. 

Note: the p-value is so close to 0 that when we calculated it, it simply returned 0.0. Hence, we are only displaying the empirical distribution of the difference in group means.


Therefore, we rejected the null hypothesis through the plot we created, and reject the hypothesis that the two groups came from the same distribution.
