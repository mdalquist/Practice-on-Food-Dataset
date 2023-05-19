# A Data Science Approach to Low Calorie, High Protein, Tasty Foods


## Introduction

Food is a fundamental part of life. It provides us with the energy needed to sustain our bodily functions and carry out daily activities. Food also has a significant impact on our brains and mental health, as eating tasty food brings us joy and generally makes life worth living. However, just eating food that keeps us fed and happy isn't always the best choice. Eating healthy food is extremely important in our overall well-being, and eating healthy, tasty food should be what everyone strives to do with every meal. While there is more that goes into what makes a food healthy or not, I will be measuring the quality of the food in this study by CPP (calories per gram of protein) and the rating of the food based on surveys from food.com. Lower values of CPP will be healthier foods, as those values will indicate high protein, low calorie foods, which help lower fat and build muscle. 

Research Question: Are the recipes with low CPPs generally better or worse tasting as a sample than the rest of the recipes in the database? Identify the 20 recipes with the lowest CPPs and view their ratings. 

Datasets: There are two datasets I will be using, both from [food.com](https://www.food.com/). The first is the recipes dataset, which contains recipes contributors of food.com have posted to the website ([example](https://www.food.com/recipe/chickpea-and-fresh-tomato-toss-51631)). The second dataset contains reviews and ratings for the recipes in the first dataset. The recipes dataframe has 83782 rows and 12 columns, while the reviews dataframe has 731927 rows and 5 columns. These two dataframes will be joined by their 'id' columns. Other significant columns include the 'nutrition' column in the recipes dataframe, which contains the food's health information such as calories, fat, protein, and carbs, and the 'rating' column in the reviews dataframe, which will be used to assess the quality of each recipe.


## Cleaning and EDA

### Data and Cleaning

Loading in the two dataframes. A sample of the two are seen below:

Recipes:

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        | nutrition                                |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | description                                                                                                                                                                                                                                                          | ingredients                                                                                                                                                                    |   n_ingredients |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting'] | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven! | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |

Reviews:

|   user_id |   recipe_id | date       |   rating | review                                                                                                                                                                                                        |
|----------:|------------:|:-----------|---------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   1293707 |       40893 | 2011-12-21 |        5 | So simple, so delicious! Great for chilly fall evening. Should have doubled it ;)<br/><br/>Second time around, forgot the remaining cumin. We usually love cumin, but didn't notice the missing 1/2 teaspoon! |\n|    126440 |       85009 | 2010-02-27 |        5 | I made the Mexican topping and took it to bunko.  Everyone loved it.                                                                                                                                          |
|     57222 |       85009 | 2011-10-01 |        5 | Made the cheddar bacon topping, adding a sprinkling of black pepper. Yum!                                                                                                                                     |

Next is merging the two dataframes, matching the 'id' column of the recipe dataframe with the 'recipe_id' column of the review dataframe. The resulting dataframe wasn't ready to be analyzed yet, so I altered a few things to help the process moving forward. First, I filled in all values of 0 for the rating with NaN. The 0 ratings were likely added to the dataframe to signal there was no rating given on that review, so to avoid skewing the averages, the 0 ratings were replaced by null values. Secondly, I found the average rating for each recipe and added the data to a new column to the merged dataframe called 'average rating'. A sample of the resulting dataframe is shown below.

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        | nutrition                                |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | description                                                                                                                                                                                                                                                          | ingredients                                                                                                                                                                    |   n_ingredients |   user_id | date       | review                                                                                                                                                                                                                                                         |   average rating |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|----------:|:-----------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting'] | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven! | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |    386585 | 2008-11-19 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs. |                4 |

Now that the full dataset is put together, and the average rating column is added, the dataset can be simplified to only include the variables needed for our analysis. To start, the calorie and protein information must be isolated. The nutrition column contains strings that look like lists, with the first 'entry' in the list being number of calories and the fifth 'entry' being calories in percent of daily value. The recommended daily value by the CDC is 50 grams, so the true value for grams of protein can be estimated by multiplying the percent of daily value by 0.5 (divide the percent by 100 to get a decimal, then multiply by 50 for grams of protein - easier to do in one step). CPP (calories per gram of protein) is calculated by dividing the calorie column by the protein column. The calories, protein, and CPP are all added to the dataframe as new columns. These three columns, along with the 'name', 'id', and 'average rating' columns, are selected from the main dataframe to remove data insignificant to our analysis.

The next step is finding and removing null and unwanted values. The first issue is having 'inf' in the CPP column, which results when there the protein content is 0 (pandas sometimes handles dividing by 0 by displaying the result as infinity). These values are replaced by NaN. There is one row with a null value for the name, so I cannot identify what the food is. All rows with no rating are dropped; if there are no ratings, then we cannot measure taste in this study, so those values are not included. The row with no name was dropped with due to it also not having a rating, so any potential issue there was resolved. I suspected that all null values in the CPP column were because the protein content was zero, as this was either manually handled earlier or handled by pandas. This was true, as all rows with NaN in 'CPP' also were 0 in 'protein'. Most of the recipes with 0 protein were either spices or cleaning solutions, so it made sense to drop all these rows as well because they're not food. Here is a sample of the resulting clean dataframe:

| name                                 |     id |   calories |   protein |     CPP |   average rating |
|:-------------------------------------|-------:|-----------:|----------:|--------:|-----------------:|
| 1 brownies in the world    best ever | 333281 |      138.4 |       1.5 | 92.2667 |                4 |
| 1 in canada chocolate chip cookies   | 453467 |      595.1 |       6.5 | 91.5538 |                5 |
| 412 broccoli casserole               | 306168 |      194.8 |      11   | 17.7091 |                5 |
| millionaire pound cake               | 286009 |      878.3 |      10   | 87.83   |                5 |
| 2000 meatloaf                        | 475785 |      267   |      14.5 | 18.4138 |                5 |

### Univariate Analysis

When doing any data analysis, looking at the distribution of variables can reveal crucial information about the data. Here are histograms for the two important variables in this study: CPP and Average Rating.

<iframe src="cpp_hist.html" width=800 height=600 frameBorder=0></iframe>

The CPP variable is heavily skewed to the right, with much of the data existing close to 0 and a large tail going off to the right. This version of the histogram is zoomed in for a better view, but the CPP gets all the way out to over 5000 for one recipe. Also, there are 26 recipes with a CPP of 5 or less, which will be the targets of finding the 20 recipes with the lowest CPP. There are also a significant amount of recipes with 10 or lower CPP, which will be the 'healthy' foods used to compare to the rest of the sample for determining if the healthy recipes are better or worse tasting than the others.

<iframe src="rating_hist.html" width=800 height=600 frameBorder=0></iframe>

The ratings variable is also skewed, this time to the left. Much of the data is bunched towards the higher rankings, and the majority of the rankings are 4 or higher. The people seem to like the recipes posted on food.com

### Bivariate Analysis

While our research question is geared towards the hypothesis test that will be conducted at the end of the study, the relationship between our two main variables, CPP and Average Rating, should still be investigated

<iframe src="cpp_rating_scatter.html" width=800 height=600 frameBorder=0></iframe>

The frequency of the average ratings at whole numbers makes the plot a bit messy, but there doesn't seem to be a strong relationship between the two variables. The low CPP foods are spread out among all the ratings. However, most of the high CPP foods seem to be rated higher, likely because they are sugary and fatty foods or desert foods that people tend to like more.

### Aggregation

Another way to see the spread of the CPP values across the ratings is by taking the mean nutrition values at each rating.

|   average rating |   calories |   protein |     CPP |
|-----------------:|-----------:|----------:|--------:|
|              1   |    459.861 |   15.369  | 56.4874 |
|              1.5 |    451.825 |   18.3125 | 54.8254 |
|              2   |    472.92  |   16.3826 | 56.3859 |
|              2.5 |    405.958 |   14.3125 | 47.3341 |
|              3   |    451.72  |   17.6142 | 53.1963 |
|              3.5 |    433.296 |   17.739  | 52.5921 |
|              4   |    435.209 |   18.4661 | 48.5643 |
|              4.5 |    431.302 |   17.751  | 53.3777 |
|              5   |    451.062 |   17.2078 | 57.5536 |

This table also shows no trend between average rating and the nutrition variables. The average CPP remains relatively constant for all ratings.

### 20 Lowest CPP Recipes

After the data cleaning and initial analysis, we have enough information to answer one of our questions: What are the 20 recipes with the lowest CPP? What are the calorie and protein counts, and more importantly, are they any good? Here are the 20 recipes with the lowest CPP:

| name                                     |     id |   calories |   protein |     CPP |   average rating |
|:-----------------------------------------|-------:|-----------:|----------:|--------:|-----------------:|
| jello fruit bars                         | 430981 |       23.4 |       5.5 | 4.25455 |          5       |
| summer vegetable   fish bundles          | 379592 |      189   |      41   | 4.60976 |          4       |
| gluten free crispy fried fish            | 461462 |      380.8 |      82   | 4.6439  |          3.5     |
| homemade sugar free jell o  jello        | 318524 |       14.1 |       3   | 4.7     |          4.66667 |
| turkey sausage patties                   | 308359 |       88.4 |      18.5 | 4.77838 |          5       |
| herb crusted turkey tenderloin           | 432153 |      168.2 |      35   | 4.80571 |          4.66667 |
| baked mahi mahi                          | 456285 |      201.9 |      42   | 4.80714 |          4.76923 |
| cooking boneless skinless chicken breast | 381016 |      129.8 |      27   | 4.80741 |          4.5     |
| orange soda chicken                      | 276889 |      129.8 |      27   | 4.80741 |          3       |
| easiest chicken ever                     | 310923 |      173.1 |      36   | 4.80833 |          5       |
| quick   easy italian style cod fillets   | 378670 |      194.8 |      40.5 | 4.80988 |          4.5     |
| 0 carb   0 cal gummy worms               | 283618 |      384.7 |      79.5 | 4.83899 |          4.75    |
| crouton crusted chicken                  | 367071 |      135.5 |      28   | 4.83929 |          5       |
| super quick prosciutto wrapped fish      | 321766 |      136.1 |      28   | 4.86071 |          4.83333 |
| italian zest chicken  zip and steam      | 287979 |      131.3 |      27   | 4.86296 |          4.5     |
| caesar chicken breast                    | 326706 |      131.4 |      27   | 4.86667 |          5       |
| egg whites scrambled with egg sustitute  | 434067 |       97.4 |      20   | 4.87    |          5       |
| easy chili lime tilapia                  | 512798 |      221.7 |      45.5 | 4.87253 |          5       |
| most delicious egg white omelette ever   | 417393 |       87.9 |      18   | 4.88333 |          4.5     |
| snow crab legs                           | 298380 |      408.2 |      83.5 | 4.88862 |          4.88889 |

There's some real potential here. Chicken, fish, and even some deserts that seem pretty tasty. Lots of good ratings as well.
I have no idea what 'orange soda chicken' is and I will never find out. That sounds terrible, and the 3-star rating isn't giving me any hope.

## Assessment of Missingness

To assess missingness in the data, I will be looking at the original dataframe after merging the recipe and review dataframes, replacing 0 ratings with null values, and creating the average rating column

### NMAR Analysis

In the merged dataframe, there are only three columns with missing values: 'description' - the description of the recipe by the contributior, 'review' - the review of the recipe by a commenter, and 'average rating'. The one column I believe is NMAR is average rating. Even though it was part of the design to change 0 ratings to NaN, the presence of non-reviews make the column NMAR because the reviewers chose not to give a rating, and therefore the data is missing due to the data itself, not other columns. More information about the commenters who place reviews on food.com, along with the existing data of date and time of the reivew, could lead the data to being MAR, as more information could explain why the data is missing.

### Missingness Dependency

After some investigation into the other missing columns, it seems that the date that the post was submitted could have something to do with description missingness, as the missing values seem to have earlier post dates. Perhaps the description feature wasn't as popularly used until later on. Similarly with the missingness for reviews, the null values in this column seem to have later dates. A possible explanation for this observation is that the newer posts have been tried less freqently, and therefore people have not had as many chances to review them. The missingness of these columns will be assessed through permutation tests. These tests were conducted by finding the average year of the missing description posts and the missing reviews, and comparing those means to the means of 1000 random resamples of the same size from the original data. The null distribution and observed values of the tests are plotted below:

<iframe src="desc_ndist.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="rev_ndist.html" width=800 height=600 frameBorder=0></iframe>

Another possibility for explanation of a missing review is the time it takes to prepare the food. Perhaps cooks spending more time making the food have less time to leave a review on the website, and are less likely to leave a review. The same test as above was ran again for the 'minutes' column containing the time it takes to prepare the recipe.

<iframe src="rev_min_ndist.html" width=800 height=600 frameBorder=0></iframe>

Descripton - missing at random - based on the missingness test conducted above, the null hypothesis that the 'description' column did not have missing values dependent on the 'submitted' column was rejected, giving evidence that the missing values in 'description' are MAR dependent on at least the 'submitted' column. The p-value of the test was 0.002 which is significant at the 0.01 level. 

Review - missing at random - the null hypothesis for this missingness test was also rejected, there is evidence that the missing values in the'review' column are dependent on the 'date' column. The p-value of the test was 0. The test was repeated for dependency on the 'minutes' column, and the result was failing to reject the null hypothesis (p-value: 0.103) - no evidence for dependency.

## Hypothesis Testing

Lastly, I will perform a Hypothesis test to see if there is any difference in ratings between healthy foods and other foods. The "healthy" foods will be all recipes that have a CPP of 10 or under. I chose 10 because when I am deciding what foods to make and eat, I usually use the ratio of 10 calories for every gram of protein to ensure I have a meal with enough protein and not too many calories. 

Null Hypothesis: There is no difference in rating between recipes in the healthy group and the overall population of recipes in the dataset, they are drawn from the same distribution.

Alternative Hypothesis: There is a difference in rating between recipes in the healthy group and the overall population of recipes.

In order to carry out this test, I will first isolate the recipes with a CPP of 10 or under and calcluate the mean average rating. Then, I will generate the null distribution by taking 1000 resamples of the same size and calculate the mean average rating for each. The result will be determined by the p-value of the observed statistic. Here is the resulting null distribution with the observed statistic plotted on it:

<iframe src="null_dist.html" width=800 height=600 frameBorder=0></iframe>

Result: Fail to reject the null hypothesis that there is a difference in the ratings for healthy recipes versus the rest of the population of recipes. The p-value came out to 0.102, which is not significant enough to give evidence the sample of recipes with CPP 10 or under have significantly higher or lower ratings that other groups of recipes.