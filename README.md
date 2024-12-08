# Recipe for Success
## Predicting the Number of Steps in Recipes on Food.com
By Matthew Yeh

## Introduction

Have you ever wondered how many steps a recipe has before you start cooking? This project aims to predict the number of steps in a recipe based on features such as its average rating and the number of minutes it takes to make.

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

3. Cleaning the data: I removed any rows with missing values in the `avg_rating`column. There were no missing values in any other quantitative columns, so I did not need to remove any other rows.



<!-- Embed at least one plotly plot you created in your notebook that displays the distribution of a single column (see Part 2: Report for instructions). Include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present, and how they answer your initial question. (Your notebook will likely have more visualizations than your website, and that’s fine. Feel free to embed more than one univariate visualization in your website if you’d like, but make sure that each embedded plot is accompanied by a description.) -->

<iframe
  src="assets/file-name.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<!-- Embed at least one plotly plot that displays the relationship between two columns. Include a 1-2 sentence explanation about your plot, making sure to describe and interpret any trends present and how they answer your initial question. (Your notebook will likely have more visualizations than your website, and that’s fine. Feel free to embed more than one bivariate visualization in your website if you’d like, but make sure that each embedded plot is accompanied by a description.) -->

<iframe
  src="assets/file-name.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<!-- Embed at least one grouped table or pivot table in your website and explain its significance. -->



## Framing a Prediction Problem

<!-- Clearly state your prediction problem and type (classification or regression). If you are building a classifier, make sure to state whether you are performing binary classification or multiclass classification. Report the response variable (i.e. the variable you are predicting) and why you chose it, the metric you are using to evaluate your model and why you chose it over other suitable metrics (e.g. accuracy vs. F1-score). -->

<!-- Note: Make sure to justify what information you would know at the “time of prediction” and to only train your model using those features. For instance, if we wanted to predict your Final Exam grade, we couldn’t use your Portfolio Homework grade, because we (probably) won’t have the Portfolio Homework graded before the Final Exam! Feel free to ask questions if you’re not sure. -->

## Baseline Model

<!-- Describe your model and state the features in your model, including how many are quantitative, ordinal, and nominal, and how you performed any necessary encodings. Report the performance of your model and whether or not you believe your current model is “good” and why.

Tip: Make sure to hit all of the points above: many Portfolio Homeworks in the past have lost points for not doing so. -->

My baseline model is a linear regression model that uses the following features:
- `avg_rating`: the average rating of the recipe
- `minutes`: the number of minutes it takes to make the recipe

Both of these features are quantitative. The performance of the model is as follows:
Mean Squared Error: 39.79106912197526
R^2 Score: 0.0005333644176367391

## Final Model

<!-- State the features you added and why they are good for the data and prediction task. Note that you can’t simply state “these features improved my accuracy”, since you’d need to choose these features and fit a model before noticing that – instead, talk about why you believe these features improved your model’s performance from the perspective of the data generating process. -->

<!-- Describe the modeling algorithm you chose, the hyperparameters that ended up performing the best, and the method you used to select hyperparameters and your overall model. Describe how your Final Model’s performance is an improvement over your Baseline Model’s performance. -->

<!-- Optional: Include a visualization that describes your model’s performance, e.g. a confusion matrix, if applicable. -->