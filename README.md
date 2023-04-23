# DSAI_Project
---
## Introduction

![LOLCover](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/LOLCover.jpg)

<br/>
League of Legends (LoL) is a highly popular multiplayer online battle arena game with an enormous user base of over 100 million active players worldwide. Riot Games, the game's publisher, hosts professional competitions all year round, attracting millions of viewers. In fact, the 2016 World Championships alone drew in 43 million viewers, surpassing the viewership of the 2016 NBA finals Game 7, which had 30.8 million viewers. Once you start playing LoL, it's easy to see why it's so addictively fun. In the main game, ten players are divided into two teams, blue or red, with the goal of destroying the opposing team's Nexus, which is the main building in their base camp. Collaboration between teammates is crucial to advance on the enemy Nexus while defending one's own, making the game play wonderfully complex.

---
<br />

## About

<br />
This is our mini-project for SC1015, Introduction to Data Science & Artificial Intelligence. Our source code:
<br />

[SC1015](https://github.com/Albert481/DSAI_Project/blob/main/SC1015_fin.ipynb)

Chosen Dataset:
[Dataset from Kaggle](https://www.kaggle.com/datasets/bobbyscience/league-of-legends-diamond-ranked-games-10-min?select=high_diamond_ranked_10min.csv)

<br/>

**Motivation & Problem definition**

*Are we able to predict the outcome of a game based on the early stages?*
<br/>

*What should we do in the early game to maximize our chances of winning?*

---
<br />

## Machine Learning & Optimization

We chose Logistic Regression over other ML models because it is a simple and interpretable model that works well with binary classification problems.


### Classification Report: Logistic Regression with all 32 variables <br/>

Initially we trained the model with all 32 variables and found that there are many variables are insignificant. We need to reduce the number of variables.

|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.72          | 0.71       | 0.71         | 210         |
| 1            | 0.71          | 0.72       | 0.71         | 206         |
| Accuracy     |               |            | 0.71         | 416         |
| Macro Avg    | 0.71          | 0.71       | 0.71         | 416         | 
| Weighted Avg | 0.71          | 0.71       | 0.71         | 416         |

<br/>
We use RFECV method to automatically select the optimal number of features for our model. Instead of specifying the number of features to select in advance with n_features_to_select, we use cross-validation to select the optimal number of features based on the performance of the model, to coarse tune the independent variables from 32 to 9. 


### Classification Report: Logistic Regression with 9 variables <br/>

Below is the classification report for model we trained with the 9 features selected by the RFECV.

|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.74          | 0.73       | 0.74         | 210         |
| 1            | 0.73          | 0.72       | 0.74         | 206         |
| Accuracy     |               |            | 0.74         | 416         |
| Macro Avg    | 0.74          | 0.74       | 0.74         | 416         | 
| Weighted Avg | 0.74          | 0.74       | 0.74         | 416         |



<br/>

We know that the logistic regression model outputs probabilities (values between 0 to 1). In order to make predictions on train data (y_train_pred), we need to convert these probabilities to 1s and 0s. For this we need to decide a threshold probability value above which all the probability values will be classified as 1 and the values below it will be classified as 0. For that, we need to plot the ROC (Receiver Operating Characteristic) curve.

An ROC curve is nothing but a graph of True Positive Rate (TPR) v/s False Positive Rate (FPR) at different classification thresholds. A higher AUC indicates better predictions, as it measures the entire two-dimensional area underneath the entire ROC curve. By lowering the classification threshold, more items are classified as positive, resulting in an increase in both False Positives and True Positives.

<br/>
<br/>

![ROC1](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/ROC1.png)

<br/>
<br/>


## Improvement
Could we reduce the features and improve the model? <br/>

Since League of Legends is a Team vs Team game, it only makes sense to take the Net data and the same blue & red features should be present. This can be easily obtain using the values we created through feature engineering. <br/>


We set up the model to have 7 predictors. We can utilize their coefficients to determining how much impact one value will have on a player's winning chances

```
df_train['blueTeamWardRetentionRatio'] = (df_train.blueWardsPlaced - df_train.redWardsDestroyed)/df_train.blueWardsPlaced
df_train['blueTeamNetKills'] = (df_train.blueKills - df_train.redKills)
df_train['blueTeamJungleMinionsKilledDiff'] = (df_train.blueTotalJungleMinionsKilled - df_train.redTotalJungleMinionsKilled)
df_train['blueTeamMinionsKilledDiff'] = (df_train.blueTotalMinionsKilled - df_train.redTotalMinionsKilled)
df_train['blueTeamAvgLevelDiff'] = (df_train.blueAvgLevel - df_train.redAvgLevel)
df_train['blueTeamCsPerMinuteDiff'] = (df_train.blueCSPerMin - df_train.redCSPerMin)
df_train['blueTeamGoldPerMinuteDiff'] = (df_train.blueGoldPerMin - df_train.redGoldPerMin)
```

<br />

### Classification Report: Random Forest Classification 

*Trying out different models*
<br/>
Over here we experiment different models. We tried the Random Forest Classifier and fine tune the hyperparameters to produce an improved accuracy result.

|              | **Precision** | **Recall** | **F1-Score** | **Support** |
|--------------|---------------|------------|--------------|-------------|
| 0            | 0.81          | 0.81       | 0.81         | 179         |
| 1            | 0.78          | 0.78       | 0.78         | 153         |
| Accuracy     |               |            | 0.80         | 332         |
| Macro Avg    | 0.79          | 0.79       | 0.79         | 332         | 
| Weighted Avg | 0.80          | 0.80       | 0.80         | 332         |

<br/>

This improved the ROC Curve significantly.

![ROC2](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/ROC2.png)

<br />


### Feature Importance

Important features derived from Random Forest

![featureimportance](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/featureimportance.png)

---
<br />
<br />

### Recommendations
Gold differential between both teams is the most important indicator for a team’s success. Therefore:

**Aim to obtain a gold lead in the early stages of the game through means such as first blood and dragon**

**Maintain said gold lead**

## Final Note

Our project successfully addressed the problem of predicting the outcome of a League of Legends game based on various in-game metrics. By utilizing machine learning techniques such as Recursive Feature Elimination and the Random Forest Classifier, we were able to identify key features and achieve a significantly higher accuracy rate than previous models.

Our data-driven insights suggest that the gold differential between both teams is the most important indicator for a team’s success. This means that the goal should be to obtain a gold lead in the early game through means such as obtaining first blood and first dragon, and then maintaining the lead throughout the game. By focusing on these key indicators of success, players can increase their chances of winning and improving their overall gameplay.

Furthermore, while our analysis was limited to the Diamond 1 - masters, it is likely that players from other Elos exhibit different gameplay patterns that may require further investigation.


<br />
<br />

Model Used: Logistic Regression, Random Forest Classifier<br/>
Others: RFECV, ROC Curve

<br />

**What did we learn fromt this project?**

- Min-max normalization <br/>
- Logistic regression<br/>
- Collaborating on Google colab<br/>
- Various scikit learn modules (Classification report, ROC curve, StratifiedKFold)


### Contributors
[Clarence](https://github.com/clarenve) - Data Scraping, Data Preparation, Exploratory Data Analysis, Feature Engineering<br>
[Albert Yu](https://github.com/Albert481) - Machine Learning, Core Analysis, Improvement<br>


#### <br>
---
