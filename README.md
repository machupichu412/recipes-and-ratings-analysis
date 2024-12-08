# Recipe for Success
## Predicting the Number of Steps in Recipes on Food.com
By Matthew Yeh

## Introduction

Have you ever wondered how many steps a recipe has before you start cooking? This project aims answer the question, "What is the relationship between recipe features such as the number of ingredients and average rating with the number of steps in the recipe?" The project aims to predict the number of steps in a recipe based on features such as its average rating and the number of minutes it takes to make.

The dataset this project is based on contains recipes and ratings from [food.com](https://www.food.com/search/?ref=nav). It was originally scraped and used by the authors of [this](https://cseweb.ucsd.edu/~jmcauley/pdfs/emnlp19c.pdf) recommender systems paper.

The raw dataset has 83782 recipes. The columns that are relevant to this project are:
- `avg_rating`: the average rating of the recipe, computed from the user ratings
- `minutes`: the number of minutes it takes to make the recipe
- `n_steps`: the number of steps in the recipe
- `n_ingredients`: the number of ingredients used in the recipe
- `nutrition`: the nutritional information of the recipe, including calories, total fat, sugar, and sodium

## Data Cleaning and Exploratory Data Analysis

<!-- Describe, in detail, the data cleaning steps you took and how they affected your analyses. The steps should be explained in reference to the data generating process. Show the head of your cleaned DataFrame (see Part 2: Report for instructions). -->

The data cleaning process involved a number of steps:
1. Assembling the dataset: The dataset was split into two files, so I had to merge them into a single DataFrame.

This involved loading the data from the recipe file, which contained the recipe ID, name, and other information, and the rating file, which contained reviews and ratings for the recipes. I replaced ratings of 0 with NaN values, as ratings range from 1 to 5 stars, meaning 0 ratings indicated invalid or missing data. This ensured that the average ratings would not be skewed by 0 ratings. I then merged the two DataFrames on the recipe ID. I then used this merged DataFrame to calculate the average rating of each recipe by grouping by the recipe ID and taking the mean of the ratings.

2. Creating new columns based off of the `nutrition` column: The `nutrition` column contained lists of nutritional information for each recipe. I extracted the calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates from the lists and created new columns for each of these values. I then removed the `nutrition` column from the DataFrame.

3. Cleaning the data: I removed any rows with missing values in the `avg_rating`column. There were no missing values in any other quantitative columns, so I did not need to remove any other rows. I did not impute any missing values, as it would not make sense to impute the average rating of a recipe.

4. Exploratory Data Analysis: I created a number of visualizations to explore the relationships between the features in the dataset. I found that recipes that take longer to make tend to have more steps, and that recipes with more ingredients tend to have more steps.

<!-- Embed at least one plotly plot you created in your notebook that displays the distribution of a single column (see Part 2: Report for instructions). Include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present, and how they answer your initial question. (Your notebook will likely have more visualizations than your website, and that’s fine. Feel free to embed more than one univariate visualization in your website if you’d like, but make sure that each embedded plot is accompanied by a description.) -->

<iframe
  src="assets/n_step_dist.html"
  width="800"
  height="600"
  frameborder="0"
  display= "block"
  margin="0"
  padding="0"
></iframe> The plot above shows the distribution of the number of steps in the recipes. The distribution is right-skewed, with most recipes having fewer than 20 steps but some recipes having far more, with an extreme outlier at 100 steps. The mode is 7 steps, with 6,846 recipes having 7 steps. This indicates that most recipes are relatively simple to make, with only a few steps involved.

<!-- Embed at least one plotly plot that displays the relationship between two columns. Include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present and how they answer your initial question. (Your notebook will likely have more visualizations than your website, and that’s fine. Feel free to embed more than one bivariate visualization in your website if you’d like, but make sure that each embedded plot is accompanied by a description.) -->

<iframe
  src="assets/N_Steps_vs_N_Ingredients.html"
  width="800"
  height="600"
  frameborder="0"
  display= "block"
  margin="0"
  padding="0"
></iframe>
This plot shows the relationship between the number of steps and the number of ingredients in the recipes. There is generally a positive relationship between the two variables, with recipes with more ingredients tending to have more steps. This makes intuitive sense, as more ingredients would likely require more steps to prepare.

<!-- Embed at least one grouped table or pivot table in your website and explain its significance. -->
Next, I created a table aggregating the average rating, calories, and number of steps for recipes in different time ranges:

| time_range (mins)   |   avg_rating |   calories (#) |   n_steps |
|:--------------------|-------------:|---------------:|----------:|
| <15                 |      4.67088 |        313.495 |   5.54733 |
| 15-30               |      4.62338 |        375.643 |   9.33545 |
| 30-60               |      4.60655 |        445.806 |  11.4932  |
| 1-2 hrs             |      4.62744 |        558.003 |  13.1389  |
| 2+ hrs              |      4.59355 |        553.625 |  12.3121  |


The table shows that recipes that take longer to make tend to have lower ratings, more calories, and more steps.

## Framing a Prediction Problem

<!-- Clearly state your prediction problem and type (classification or regression). If you are building a classifier, make sure to state whether you are performing binary classification or multiclass classification. Report the response variable (i.e. the variable you are predicting) and why you chose it, the metric you are using to evaluate your model and why you chose it over other suitable metrics (e.g. accuracy vs. F1-score). -->

Based on the analysis in the previous section, there appear to be relationships between the number of steps in a recipe and features such as the time in minutes to make, the average rating, the number of ingredients, and the number of calories. I will use these features to predict the number of steps in a recipe, all of which would be known at the time of prediction for a given recipe from food.com.

I will be predicting the number of steps in a recipe, which is a regression problem. The response variable is `n_steps`, which is a quantitative variable. I chose this variable because it is likely to be related to the complexity of the recipe and can be predicted using the other features in the dataset.

I will be using the mean squared error as the metric to evaluate my model. I chose this metric because it penalizes large errors more than the mean absolute error, which is important because I want to minimize the number of large errors in my predictions. The mean squared error is also easier to interpret than other metrics such as the mean absolute percentage error, which can be difficult to interpret when the target variable has a wide range of values.

The R^2 score will also be used to evaluate the model. This metric measures the proportion of the variance in the target variable that is predictable from the features. It is a useful metric for regression problems because it provides an indication of how well the model explains the variance in the target variable.

<!-- Note: Make sure to justify what information you would know at the “time of prediction” and to only train your model using those features. For instance, if we wanted to predict your Final Exam grade, we couldn’t use your Portfolio Homework grade, because we (probably) won’t have the Portfolio Homework graded before the Final Exam! Feel free to ask questions if you’re not sure. -->

## Baseline Model

<!-- Describe your model and state the features in your model, including how many are quantitative, ordinal, and nominal, and how you performed any necessary encodings. Report the performance of your model and whether or not you believe your current model is “good” and why.

Tip: Make sure to hit all of the points above: many Portfolio Homeworks in the past have lost points for not doing so. -->

My baseline model is a linear regression model that uses the following features:
- `avg_rating`: the average rating of the recipe
- `minutes`: the number of minutes it takes to make the recipe
These were chosen based on the results from the aggregated table, which showed that recipes that take longer to make tend to have more steps. In addition, there appeared to be a slight negative relationship between the average rating and the number of steps in the recipes.

No encoding was necessary, as both of these features are already quantitative. The performance of the model is as follows:
- Mean Squared Error: 39.79106912197526
- R^2 Score: 0.0005333644176367391

The baseline model is not very good, as the R^2 score is very low and the mean squared error is relatively high, indicating that the model does not explain much of the variance in the number of steps in the recipes.

## Final Model

<!-- State the features you added and why they are good for the data and prediction task. Note that you can’t simply state “these features improved my accuracy”, since you’d need to choose these features and fit a model before noticing that – instead, talk about why you believe these features improved your model’s performance from the perspective of the data generating process. -->

<!-- Describe the modeling algorithm you chose, the hyperparameters that ended up performing the best, and the method you used to select hyperparameters and your overall model. Describe how your Final Model’s performance is an improvement over your Baseline Model’s performance. -->

The final modeling algorithm I chose is a LASSO regression model that uses the following features:
- `avg_rating`: the average rating of the recipe
- `minutes`: the number of minutes it takes to make the recipe
- `calories (#)`: the number of calories in the recipe
- `n_ingredients`: the number of ingredients in the recipe

These features were chosen because they are likely to be related to the number of steps in a recipe. For example, recipes with more ingredients may have more steps, and recipes with more calories may have more steps. The LASSO regression model was chosen because it performs feature selection by shrinking the coefficients of irrelevant features to 0. This helps prevent overfitting and makes the model more interpretable.

I log-transformed the `minute` feature to make the data more normally distributed, as it was right-skewed with some recipes taking a very long time to make (these recipes mostly involved some kind of pickling or fermentation). I also standardized the features so that they have a mean of 0 and a standard deviation of 1. This helps the model converge faster and makes the coefficients more interpretable.

To create more features, I also used polynomial transformations of the features. I used the `PolynomialFeatures` class from scikit-learn to create polynomial features of the log-transformed `minute` column and the `n_ingredients` column. This allows the model to capture non-linear relationships between the features and the target variable.

I also used a quantile transformer to transform the `calories (#)` column. This transformation helps the model better capture the relationship between the `calories (#)` column and the target variable by making the data more normally distributed.

The regularization hyperparameter that performed the best was alpha=0.03125 or $2^{-5}$. The polynomial degrees that performed the best was 5 for the `minute` column and 4 for the `n_ingredients` column.
These hyperparameters were selected using cross-validation. The final model's performance is as follows:

- Mean Squared Error: 29.4196790148663
- R^2 Score: 0.2610405235715909

The final model is an improvement over the baseline model, with a lower mean squared error and a higher R^2 score, indicating that the model explains more of the variance in the number of steps in the recipes. This increase in performance is likely due to the addition of features that are more likely related to the number of steps in a recipe, and the use of a more complex model that can capture non-linear relationships between the features and the target variable. However, there is still room for improvement, as the R^2 score is still relatively low.

<!-- Optional: Include a visualization that describes your model’s performance, e.g. a confusion matrix, if applicable. -->