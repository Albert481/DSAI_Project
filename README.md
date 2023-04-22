# DSAI_Project
---
## Introduction



![alt text](IMAGE_URL "Source: ")

---
<br />
<br />

## Motivation
We thought it would be fun to explore this data set and see what team attributes are associated with a team winning. There's a wealth of data in this dataset and it seems to be a solid exercise for general data wrangling to start, opportunities for visualizations throughout, and serves as a good primer to build and evaluate models that predict wins.

The dataset we chose contains the first 10min. stats of approx. 10k ranked games from a high ELO (DIAMOND I to MASTER). Players have roughly the same level.
<br/>
[Dataset from Kaggle] (https://www.kaggle.com/datasets/bobbyscience/league-of-legends-diamond-ranked-games-10-min?select=high_diamond_ranked_10min.csv)

<br/>
**What factors would affect the outcome of a League of Legends match? Predict the outcome of a game based on in-game values at the 10-minute mark?**

---
<br />
<br />

## Exploratory Data Analysis


## 
### Classification Report: Logistic Regression with all 42 variables
Initially we built the model with all 42 variables and found that there are many variables are insignificant. We need to reduce the number of variables.

|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.71          | 0.72       | 0.71         | 183         |
| 1            | 0.70          | 0.67       | 0.69         | 174         |
| Accuracy     |               |            | 0.70         | 357         |
| Macro Avg    | 0.70          | 0.70       | 0.70         | 357         | 
| Weighted Avg | 0.70          | 0.70       | 0.70         | 357         |

<br/>
We use RFECV method to automatically select the optimal number of features for our model. Instead of specifying the number of features to select in advance with n_features_to_select, we use cross-validation to select the optimal number of features based on the performance of the model, to coarse tune the independent variables from 42 to 28. 

<br/>
<br/>
### Classification Report: Logistic Regression with 28 variables
|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.70          | 0.76       | 0.73         | 209         |
| 1            | 0.77          | 0.72       | 0.74         | 238         |
| Accuracy     |               |            | 0.74         | 447         |
| Macro Avg    | 0.74          | 0.74       | 0.74         | 447         | 
| Weighted Avg | 0.74          | 0.74       | 0.74         | 447         |

---
<br />
<br />

A reduction from 42 to 28 features using RFECV is a good start, but whether 28 features is still too many for logistic regression depends on several factors, such as the size and complexity of your dataset, the amount of noise in the data, and the degree of multicollinearity among the features.

<br/>
We know that the logistic regression model outputs probabilities (values between 0 to 1). In order to make predictions on train data (y_train_pred), we need to convert these probabilities to 1s and 0s. For this we need to decide a threshold probability value above which all the probability values will be classified as 1 and the values below it will be classified as 0. For that, we need to plot the ROC (Receiver Operating Characteristic) curve.

An ROC curve is nothing but a graph of True Positive Rate (TPR) v/s False Positive Rate (FPR) at different classification thresholds.
<br/>
![alt text](IMAGE_URL "Source: ")

<br/>
<br/>
## Improvement
Could we reduce the features and improve the model? <br/>

We observe that there are some features which might not directly affect the outcome as it only aids in better decision making. These features are "blueWardsPlaced, blueWardsDestroyed". <br/>

Since League of Legends is a Team vs Team game, it only makes sense to take the Net data and the same blue & red features should be present. This can be easily obtain using the values we created through feature engineering. <br/>


We set up the model to have 7 predictors. We can utilize their coefficients to determining how much impact one value will have on a player's winning chances

<br />
<br />
### Classification Report: Random Forest Classification 

**Trying out different models**
Over here we experiment different models. We tried the Random Forest Classificatier and fine tune the hyperparameters to produce an improved accuracy result.
|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.84          | 0.77       | 0.80         | 183         |
| 1            | 0.78          | 0.84       | 0.81         | 174         |
| Accuracy     |               |            | 0.81         | 357         |
| Macro Avg    | 0.81          | 0.81       | 0.81         | 357         | 
| Weighted Avg | 0.81          | 0.81       | 0.81         | 357         |

<br/>
![alt text](IMAGE_URL "Source: ")

<br />
<br />
### Feature Importance
![alt text](IMAGE_URL "Source: ")

---
<br />
<br />

## Final Note



### Contributors
[Clarence](https://github.com/) - Data Scraping, Data Preparation, Exploratory Data Analysis, Feature Engineering<br>
[Albert Yu](https://github.com/Albert481) - Machine Learning, Core Analysis, Improvement<br>


#### <br>
---
