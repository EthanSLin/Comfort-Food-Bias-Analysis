Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods? This project by Beomsuk Seo and Ethan Lin for DSC 80 at UCSD aims to answer that.

# Introduction

One third of American adults are overweight, and another third are obese. This physical health crisis has many causes, each of which must be addressed accordingly. One such cause is the American obsession with comfort food. While satisfying and filling, foods like burgers, mac and cheese, and barbecue are notoriously unhealthy, contributing to the rampant obesity across the country. To analyze a potential instance of this occurring, we will look at a dataset of Food.com recipes (RAW_recipes.csv) and a dataset of Food.com reviews (RAW_interactions.csv). 

These two datasets were originally scraped by the authors of a recommender systems paper. They consisted of 83,782 and 731,927 rows respectively. The data captured in these datasets record a decade's worth of user-submitted recipes and reviews of those recipes. They present a large sample of the many home-cooked meals being prepared across America, as well as the feedback to the recipes they were cooked from. From this, we can analyze if American home-cooking is impacted by the general trend of American obsession with unhealthy foods.

The columns that we were interested in are as follows:

'RAW_recipes.csv'
| Column | Description |
|:--- | ---:|
| 'name' | Recipe name |
| 'id' | Recipe ID |
| 'nutrition' | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| 'n_steps' | Number of steps in recipe |

'RAW_interactions.csv'
| Column | Description |
|:--- | ---:|
|'recipe_id' | Recipe ID |
|'rating' | Rating given |

We then merged these two datasets, producing a dataset with 234,429 rows. In addition, for each row, we included an 'avg_rating' column that lists the average rating given to that recipe by reviewers.

With these two datasets, the question we aim to answer is: *Are recipes for unhealthy, heavy foods on Food.com rated more highly than recipes for healthier foods?*



# Cleaning and EDA

234,429



# Assessment of Missingness


# Hypothesis Testing