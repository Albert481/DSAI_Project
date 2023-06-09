# DSAI_Project
---
![LOLCover](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/LOLCover.jpg)

---

### Introduction
This is our mini-project for SC1015, Introduction to Data Science & Artificial Intelligence.

Our source code:
[SC1015](https://github.com/Albert481/DSAI_Project/blob/main/SC1015_fin.ipynb)

---

### Dataset

Chosen Dataset:
[Dataset from Kaggle](https://www.kaggle.com/datasets/bobbyscience/league-of-legends-diamond-ranked-games-10-min?select=high_diamond_ranked_10min.csv)

This dataset contains data for ranked game from Diamond 1 - Master

Size of the dataset is 9879

---

### Motivation & Problem Definition

*Are we able to predict the outcome of a game based on the early stages?*
<br/>

*What should we do in the early game to maximize our chances of winning?*

---

### Data Cleaning & Data Preparation

1. Check for completeness of data
2. Check for duplicates
3. Filtering data to fit our problem
4. Remove columns that represent same statistics (to avoid multicollinearity)
5. Remove outliers
6. Apply min-max normalization

---

### Exploratory Data Analysis
<br />

![winrate](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newwinrate.png)

We observe that win rates are about 50% for both teams. This indicates a balanced game with no advantage for being on one team or the other.

![winrateWStats](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newwinrateWStats.png)

Plotting vertical bar graphs, we see that First Blood, First Herald and First Dragon are important objectives that improve winrates to about 60%

![corre](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newcorr.png)

A heatmap helpes us visually analyse the relationships between variables. We note that blueGoldDiff and blueExperieneDiff have the highest correlation to winning games. They represent the difference in gold and experience between both teams at the 10 minute mark. We can use scatterplots to visualize this relationship.

![winsvsgold](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newwinratevsgold.png)

From this scatterplot, we observe that just having a 1000 gold lead at 10 minutes drastically increased the winrate to about 75%. It continues to increase till around a 4700 gold lead where it basically guarantees a win.

![winsvsexp](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newwinratevsexp.png)

Similarly, we can see a similar relationship with experience lead where it starts at a 75% winrate at a 1000 experience point lead, skyrocketing to a 100% win rate at around 3300 experience points.

![netkillsWin](https://github.com/Albert481/DSAI_Project/raw/main/repo_images/newnetkills.png)

Lastly, we plotted 2 boxplots to see the how the difference in kills between both teams at 10 minutes can affect the outcome of the game. Unsurprisingly, what we notice is that winning teams tend to have slightly more net kills.

---

### Machine Learning & Optimization

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


### Improvement
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
