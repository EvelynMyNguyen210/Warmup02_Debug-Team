# 1. Introduction

## 1.1. The Urgency of the Topic

In the problem of fraud transaction detection, the biggest challenge lies not only in the algorithm but also in the nature of the data. The extreme imbalance between legitimate and fraudulent transactions, along with sophisticated behaviors concealed under various payment methods, is a major concern. Machine learning models (such as Random Forest or XGBoost) can only achieve optimal performance when the input data is cleaned and accurately reflects the nature of fraudulent behavior. Therefore, feature engineering, or the process of transforming raw data into meaningful features, helps models understand anomalous patterns and significantly improves their performance.

## 1.2. Project Objectives

Instead of focusing on fine-tuning model parameters, this project focuses on optimizing the quality of input data. Specific objectives include:

**EDA (Exploratory Data Analytics).** Explore hidden relationships between variables to uncover traces of fraudulent behavior on the dataset.

**Feature Engineering.** Constructing new variables from raw data to enhance the algorithm's classification capabilities.

**Evaluate effectiveness.** Perform comparative analysis (A/B testing).aboveThis model demonstrates the importance of data processing. Specifically, in this project, the team will compare the model's performance when trained on raw data versus processed data and feature extraction.

## 1.3. Scope of the Project

The project uses a simulated dataset [PaySim](https://www.kaggle.com/datasets/ealaxi/paysim1?fbclid=IwY2xjawQt4IpleHRuA2FlbQIxMABicmlkETFRQTVMTUtUMEwzR2JFcWZKc3J0YwZhcHBfaWQQMjIyMDM5MTc4ODIwMDg5MgABHt2IJf88BNasTaUnRoEMgKLebwUkbTMWUHinH0pQVV7yfgX2wPPrvsJxk9iN_aem_mbURNxMBhP1k6Iphu4y0XA). Focusing on processes such as data cleaning, EDA, and feature engineering, then using powerful algorithms like Random Forest and XGBoost as measurement tools for verification.

# 2. Dataset Description
## 2.1. Data Structure
Each row in the dataset represents a single financial transaction. The data captures key information such as the transaction timestamp, transaction type, amount, and the account balances of both the sender and the receiver before and after the transaction.

A few sample rows are illustrated below:
#### Sample Transactions
The following examples are extracted from the PaySim dataset to demonstrate the structure of a typical transaction:
| step | type     | amount   | nameOrig     | oldbalanceOrg | newbalanceOrig | nameDest     | oldbalanceDest | newbalanceDest | isFraud | isFlaggedFraud |
|------|----------|----------|--------------|----------------|----------------|--------------|----------------|----------------|--------|----------------|
| 1    | PAYMENT  | 9839.64  | C1231006815  | 170136.0       | 160296.36      | M1979787155  | 0.0            | 0.0            | 0      | 0              |
| 1    | PAYMENT  | 1864.28  | C1666544295  | 21249.0        | 19384.72       | M2044282225  | 0.0            | 0.0            | 0      | 0              |
| 1    | TRANSFER | 181.00   | C1305486145  | 181.0          | 0.0            | C553264065   | 0.0            | 0.0            | 1      | 0              |
| 1    | CASH_OUT | 181.00   | C840083671   | 181.0          | 0.0            | C38997010    | 21182.0        | 0.0            | 1      | 0              |
| 1    | PAYMENT  | 11668.14 | C2048537720  | 41554.0        | 29885.86       | M1230701703  | 0.0            | 0.0            | 0      | 0              |

**Quick observations:**
- Fraudulent transactions (`isFraud = 1`) often occur in categories such as `TRANSFER` or `CASH_OUT`.
- Some transactions result in the sender’s balance dropping to zero immediately — a potentially suspicious pattern that models should learn to detect.

Overall, the dataset does more than store isolated transactions; it captures money flow and balance transitions, which is highly valuable for detecting anomalous behavior.

## 2.2. Feature Explanation
One of the strengths of the PaySim dataset lies in its intuitive and realistic feature design. This allows practitioners not only to train models but also to understand the underlying financial behavior reflected in the data.

The amount variable represents the transaction value and is one of the most critical features. In real-world scenarios, fraudulent transactions often involve unusual amounts — either significantly larger than typical user behavior or deliberately fragmented to avoid detection.

The step variable denotes time in hours, spanning roughly 30 days. This enables time-series analysis, such as identifying accounts that perform multiple transactions within a short period — a common fraud pattern.

Balance-related features, including `oldbalanceOrg`, `newbalanceOrig`, `oldbalanceDest`, and `newbalanceDest`, are particularly important for modeling money flow. Instead of looking at transactions in isolation, these variables capture the financial state before and after each transaction. For example, an account being drained to zero or a recipient account receiving funds without a clear prior balance can both signal suspicious activity.

Additionally, the type feature categorizes transactions into groups such as `PAYMENT`, `TRANSFER`, and `CASH_OUT`. This is useful because different transaction types carry different risk levels. In practice, `TRANSFER` and `CASH_OUT` are more frequently associated with fraudulent behavior than standard payment transactions.

Overall, these features provide not only quantitative data but also behavioral insights, forming a strong foundation for advanced feature engineering in later stages.

## 2.3. Target Variable
In this problem, `isFraud` serves as the target variable. It defines a binary classification task, where each transaction is labeled as either fraudulent (1) or legitimate (0).

While this may seem straightforward, predicting fraud is inherently challenging. A robust model must not only detect as many fraudulent transactions as possible but also minimize false alarms on legitimate ones. This is especially critical in financial systems, where excessive false positives can frustrate users and erode trust.

In essence, the task involves balancing two competing objectives: maximizing fraud detection (high recall) while maintaining acceptable precision. For this reason, evaluation metrics go beyond accuracy and include precision, recall, and F1-score.

## 2.4. Key Challenge: Class Imbalance
One of the biggest challenges in this dataset is class imbalance. Fraudulent transactions account for only a tiny fraction of the data — typically less than 1% — while the vast majority are legitimate.

This creates a misleading scenario where a model can achieve high accuracy by simply predicting all transactions as non-fraudulent. For instance, if 99% of transactions are legitimate, a model that always predicts 0 will achieve 99% accuracy, yet completely fail at detecting fraud.

As a result, accuracy is not a reliable metric in this context. Instead, recall (fraud detection capability) and F1-score (balance between precision and recall) become far more meaningful. A good model should minimize missed fraud cases, even at the cost of some false positives.

To address this issue, imbalance handling techniques are applied during preprocessing. Oversampling methods such as SMOTE can be used to increase the number of fraud samples, while undersampling reduces the number of legitimate ones. Combining these approaches helps the model better learn patterns from the minority class and improves real-world fraud detection performance.

## 2.5. Preliminary Remarks
Overall, PaySim is a well-structured and accessible dataset with sufficient information to model financial behavior effectively. It serves as a solid foundation for building baseline fraud detection systems.

However, it is important to note that PaySim is a synthetic dataset. As such, fraud patterns may be simplified compared to real-world scenarios. Additionally, it lacks important contextual features such as location, device information, and long-term behavioral history, and it does not fully capture real-time system dynamics.

These limitations should be carefully considered when evaluating the model’s applicability to real-world financial environments.

# 3. Exploratory Data Analysis (EDA)

EDA - Exploratory Data Analysis is the process of examining, visualizing, and summarizing the main characteristics of a raw dataset. EDA helps to better understand the structure, detect errors, outliers, and relationships between variables before building a model.

By conducting a basic data inspection, we observe that the dataset has the following characteristics:

- The total size of the dataset is (6362620, 11)
- The data does not contain any NaN values
- There are two columns containing object-type data: type, nameOrig, nameDest

```python
df = pd.read_csv(file_path)
df.shape
df.isnull().sum()
df.info()
```
```python
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 6362620 entries, 0 to 6362619
Data columns (total 11 columns):
 #   Column          Dtype  
---  ------          -----  
 0   step            int64  
 1   type            object 
 2   amount          float64
 3   nameOrig        object 
 4   oldbalanceOrg   float64
 5   newbalanceOrig  float64
 6   nameDest        object 
 7   oldbalanceDest  float64
 8   newbalanceDest  float64
 9   isFraud         int64  
 10  isFlaggedFraud  int64  
dtypes: float64(5), int64(3), object(3)
```

With the size (6362620, 11), we can see that this dataset is not too large but still contains a significant amount of information and features. Therefore, we need to analyze each feature to understand the relationships between them.

## 3.1. Transaction type analysis

By examining transactions labeled as Fraud and Valid, we check which transaction types are considered valid and which are considered fraudulent.

```python
# Split data into Fraud and Valid
data_new = df.copy()

fraud = data_new[data_new["isFraud"] == 1]
valid = data_new[data_new["isFraud"] == 0]

print("Fraud transactions by type: \n",fraud.type.value_counts())
print("\n Valid transactions by type: \n",valid.type.value_counts())
```

We obtain the following results:
```python
Fraud transactions by type: 
 type
CASH_OUT    4116
TRANSFER    4097
Name: count, dtype: int64

 Valid transactions by type: 
 type
CASH_OUT    2233384
PAYMENT     2151495
CASH_IN     1399284
TRANSFER     528812
DEBIT         41432
Name: count, dtype: int64
```

From the analysis, we observe that the dataset contains transaction types: `CASH_IN`, `CASH_OUT`, `PAYMENT`, `TRANSFER`, and `DEBIT`. Among these, the two transaction types identified as fraud are `CASH_OUT` and `TRANSFER`. Therefore, we will focus on analyzing fraud and valid data for these two transaction types.

```python
# Focus on TRANSFER and CASH_OUT
valid = valid[(valid["type"] == "CASH_OUT")| (valid["type"] == "TRANSFER")]
data_new = data_new[(data_new["type"] == "CASH_OUT") | (data_new["type"] == "TRANSFER")]
```

## 3.2. Balance analysis

One of the important indicators reflecting whether a transaction is legitimate or suspicious is the account balance after the transaction. For example, a normal transaction should have a balance equal to the previous balance minus the transaction amount. We calculate the transaction error for the sender account (denoted as Orig) and the receiver account (denoted as Dest):

```python
data_new['errorOrig'] = data_new['oldbalanceOrg'] - data_new['amount'] - data_new['newbalanceOrig']
data_new['errorDest'] = data_new['oldbalanceDest'] + data_new['amount'] - data_new['newbalanceDest']

# Analyze these values to see the distribution of non 0 and 0 cases
# Define threshold for "zero"
threshold = 1e-6

# Origin
data_new['isErrorOrig'] = (~np.isclose(data_new['errorOrig'], 0, atol=threshold)).astype(int)

# Destination
data_new['isErrorDest'] = (~np.isclose(data_new['errorDest'], 0, atol=threshold)).astype(int)

print("Origin Error Counts:")
print(data_new['isErrorOrig'].value_counts(normalize=True) * 100)

print("\nDestination Error Counts:")
print(data_new['isErrorDest'].value_counts(normalize=True) * 100)
```
The results are:
```python
Origin Error Counts:
isErrorOrig
1    90.518079
0     9.481921
Name: proportion, dtype: float64

Destination Error Counts:
isErrorDest
0    71.075859
1    28.924141
Name: proportion, dtype: float64
```
From the analysis, we observe that errors occur frequently in Origin; both fraud and non-fraud transactions show balance inconsistencies. Therefore, most cases appear suspicious, and using Origin may not accurately reflect the relationship between fraud and non-fraud transactions.

Meanwhile, for Destination, only 29% of cases show balance errors. This suggests that using Destination may make it easier to identify suspicious transactions.

Next, we examine cases with and without errors against fraud and non-fraud labels:

```python
pd.crosstab(data_new['isErrorDest'], data_new['isFraud'], normalize='columns') * 100
pd.crosstab(data_new['isErrorOrig'], data_new['isFraud'], normalize='columns') * 100
```

The results are:

| isErrorDest | isFraud=0 | isFraud=1 |
| :---------: | :-------: | :--------: |
| 0 | 71.166528 | 40.582004 |
| 1 | 28.833472 | 59.417996 |

| isErrorOrig | isFraud=0 | isFraud=1 |
| :---------: | :-------: | :--------: |
| 0 | 9.214408 | 99.452088 |
| 1 | 90.785592 | 0.547912 |

**Analysis of isErrorDest:**
- When error = 1:
  * Fraud: 59% -> Fraud transactions have a higher error rate
  * Non-fraud: 29% 

- When error = 0:
  * Fraud: 40%
  * Non-fraud: 71% -> Normal transactions have fewer errors

*This analysis shows that fraud transactions are often associated with balance inconsistencies at the Destination. The error rate in fraud cases is roughly twice that of normal transactions.*

-> This indicates that isErrorDest is a strong feature for distinguishing between normal and abnormal transactions. isErrorDest=1 is associated with fraud transactions, while isErrorDest=0 corresponds to non-fraud.

**Analysis of isErrorOrig:**
- With error = 1:
   * Fraud: 90% -> Normal transactions have higher error occurrence
   * Non-fraud: 9% 

- With error = 0:
  * Fraud: 99% -> Fraud transactions almost never have errors
  * Non-fraud: 0.5%

*This suggests that in fraud transactions, balance errors are nearly absent, meaning balances are carefully adjusted to avoid detection (when relying on post-transaction balance checks). In contrast, non-fraud transactions tend to have higher error rates*

-> This indicates that isErrorOrig is also a potential feature for distinguishing transactions. `isErrorOrig = 1` corresponds to non-fraud, while `isErrorOrig = 0` corresponds to fraud.

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_Rate_by_Destination_Error.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.1. Probability of fraud transactions given Destination error</em>
</p>

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_Rate_by_Origin_Error.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.2. Probability of fraud transactions given Origin error</em>
</p>

**Conclusion:** `isErrorOrig` and `isErrorDest` will be added to the dataset as features.

## 3.3. Analysis of isFlaggedFraud

`isFlaggedFraud` describes transactions exceeding 200,000 units that are flagged as fraud. This is a rule-based feature (a simulation within this dataset). We examine how many transactions are flagged under this rule:

```python
# Check how many observations are flagged as Fraud
flagged = data_new[data_new["isFlaggedFraud"] == 1]
flagged_correctly = sum(flagged["isFraud"] == 1)
flagged_wrongly = len(flagged) - flagged_correctly
total = flagged_correctly + flagged_wrongly

print(flagged_correctly," observations were flagged correctly and ", flagged_wrongly, \
      " observations were flagged wrongly for a total of ", total, " flagged observations.")

# Check how many observations where the transaction is fraudulent, the transaction is a transfer and the amount is greater than 200, 000 are in the dataset
should_be_flagged = fraud[(fraud["amount"] > 200000) & (fraud["type"] == "TRANSFER")]
print("Number of observations that should be flagged: ", len(should_be_flagged))
```

The results are:
```python
16 observations were flagged correctly and 0 observations were flagged wrongly for a total of  16  flagged observations.
Number of observations that should be flagged: 2740
```

From the analysis, only 16 cases were correctly flagged, while there are actually 2740 transactions exceeding 200,000 units that were not flagged by the system.

**Conclusion:** `isFlaggedFraud` does not accurately reflect transaction characteristics. Therefore, it is not an important feature and will be dropped.

## 3.4. Analysis of nameOrig and nameDest

nameOrig and nameDest represent the sender and receiver account names. From the analysis, these columns do not provide meaningful information for the dataset, so they will be removed.

## 3.5. Analysis of amount and step

For amount, we examine its distribution for fraud and non-fraud cases:

```python
sns.boxplot(x='isFraud', y='amount', data=data_new)
plt.yscale('log')
plt.title('Amount vs Fraud')
plt.show()
```
<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Amount_vs_fraud.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.3. Distribution of amount for fraud and non-fraud</em>
</p>

From the plot, transaction amounts range from 10<sup>5</sup> to 10<sup>7</sup>. Fraud transactions tend to have higher amounts than non-fraud, but there is still overlap between the two groups. Therefore, there is no clear separation between fraud and non-fraud based solely on amount.

Next, analyzing transactions over time, we observe that fraud occurs relatively consistently, with slight spikes around step ranges 0–100 and around 400.

For valid transactions, most occur between steps 0–60 and 100–400.

```python
bins = 50

valid.hist(column="step",color="green",bins=bins)
plt.xlabel("1 hour time step")
plt.ylabel("# of transactions")
plt.title("# of valid transactions over time")

fraud.hist(column ="step",color="red",bins=bins)
plt.xlabel("1 hour time step")
plt.ylabel("# of transactions")
plt.title("# of fraud transactions over time")

plt.tight_layout()
plt.show()
```

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/%23_of_valid_transactions_over_time.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.4. Distribution of valid transactions over time</em>
</p>

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/%23_of_fraud_transactions_over_time.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.5. Distribution of fraud transactions over time</em>
</p>

To better analyze fraud timing, we convert time into hours and examine patterns within a 24-hour cycle:

```python
data_new['hour'] = data_new['step'] % 24
sns.barplot(x='hour', y='isFraud', data=data_new)
plt.title('Fraud Rate by Hour')
plt.show()
```

<p align="center">
  <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/Fraud_rate_by_Hour.bmp style="margin: 0 auto; display: block;"><br/>
  <em>Figure 3.6. Distribution of fraud transactions by hour (24h)</em>
</p>

The plot shows that fraud transactions mainly occur between 2 AM and 8 AM. Therefore, this is an effective feature for distinguishing fraud from valid transactions.

From the analysis, amount remains an important feature since fraud transactions tend to involve larger amounts. While step alone does not provide clear insights, converting it into a 24-hour feature reveals meaningful patterns. Therefore, we will add the hour column and drop the step column.

|         |     type |     amount | oldbalanceOrg | newbalanceOrig | oldbalanceDest | newbalanceDest | isFraud |  errorOrig |     errorDest | isErrorOrig | isErrorDest | hour |
|---------|---------:|-----------:|--------------:|---------------:|---------------:|---------------:|--------:|-----------:|--------------:|------------:|------------:|-----:|
|    2    | TRANSFER |     181.00 |        181.00 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  1.810000e+02 |           0 |           1 |    1 |
|    3    | CASH_OUT |     181.00 |        181.00 |            0.0 |       21182.00 |           0.00 |       1 |       0.00 |  2.136300e+04 |           0 |           1 |    1 |
|    15   | CASH_OUT |  229133.94 |      15325.00 |            0.0 |        5083.00 |       51513.44 |       0 | -213808.94 |  1.827035e+05 |           1 |           1 |    1 |
|    19   | TRANSFER |  215310.30 |        705.00 |            0.0 |       22425.00 |           0.00 |       0 | -214605.30 |  2.377353e+05 |           1 |           1 |    1 |
|    24   | TRANSFER |  311685.89 |      10835.00 |            0.0 |        6267.00 |     2719172.89 |       0 | -300850.89 | -2.401220e+06 |           1 |           1 |    1 |
|   ...   |      ... |        ... |           ... |            ... |            ... |            ... |     ... |        ... |           ... |         ... |         ... |  ... |
| 6362615 | CASH_OUT |  339682.13 |     339682.13 |            0.0 |           0.00 |      339682.13 |       1 |       0.00 |  0.000000e+00 |           0 |           0 |   23 |
| 6362616 | TRANSFER | 6311409.28 |    6311409.28 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  6.311409e+06 |           0 |           1 |   23 |
| 6362617 | CASH_OUT | 6311409.28 |    6311409.28 |            0.0 |       68488.84 |     6379898.11 |       1 |       0.00 |  1.000000e-02 |           0 |           1 |   23 |
| 6362618 | TRANSFER |  850002.52 |     850002.52 |            0.0 |           0.00 |           0.00 |       1 |       0.00 |  8.500025e+05 |           0 |           1 |   23 |
| 6362619 | CASH_OUT |  850002.52 |     850002.52 |            0.0 |     6510099.11 |     7360101.63 |       1 |       0.00 |  9.313226e-10 |           0 |           0 |   23 |

<p align="center">
  <em>Table 3.1 Processed dataset</em>
</p>

# 4. Data Preprocessing
## 4.1. Data Encoding

Before feeding data into the model, feature values must be converted into numerical form. In the current dataset, the type column is the only column containing string values (`CASH_OUT`, `TRANSFER`). Here, we use one-hot encoding to convert it into numerical data.

```python
# Seperate feature (X) and target (y)
X = data_new.drop(columns=['isFraud'])
y = data_new['isFraud']

# Apply one hot encoding to type feature
X = pd.get_dummies(X, columns=['type'], drop_first=True)
```

## 4.2. Train-test split

Before training, we split the dataset into two parts: train and test. This ensures the model can generalize to new data and prevents overfitting. It ensures the model learns patterns instead of memorizing data.

We split 80% for training and 20% for testing. We also set `stratify=y` to maintain class distribution, which is crucial because the dataset is highly imbalanced (fraud rate ~0.1%).

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.2,       # 80% train, 20% test
    random_state=42,
    stratify=y           # VERY IMPORTANT for imbalance
)
```

## 4.3. Handling imbalance

Since the dataset is highly imbalanced, the model may fail to detect fraud if not handled properly. Therefore, we apply oversampling.

SMOTE (Synthetic Minority Over-sampling Technique) is a machine learning technique used to handle imbalanced datasets by generating synthetic samples for the minority class. Instead of duplicating data, SMOTE interpolates between nearby data points, helping the model learn better and reducing overfitting.

We apply SMOTE to the training set:

```python
from imblearn.over_sampling import SMOTE

smote = SMOTE(random_state=42)
X_train_res, y_train_res = smote.fit_resample(X_train, y_train)
```

# 5. Model selection

For the fraud detection problem, we chose a supervised learning approach because the dataset already comes with clear labels (fraud = 1, non-fraud = 0). This allows the model to learn directly from existing patterns and make predictions on new data.

After some research, our team decided to focus on two main models:

- Random Forest
- XGBoost (Extreme Gradient Boosting)

## 5.1 Random Forest

If you have already worked with Machine Learning, Random Forest will probably feel like one of those “safe and familiar” choices. In anomaly detection or fraud detection tasks, the data is often noisy and complex, especially because of the severe class imbalance. Random Forest handles this by building a “forest” of decision trees and making predictions based on majority voting. As a result, the model is less likely to overfit and is relatively stable in the presence of outliers.

Another huge advantage is that Random Forest can provide Feature Importance, which shows how important each feature is. This matters a lot in practice, because it helps explain to the business side why the system flagged a transaction as fraud. Was it because of an unusual amount, or because the card swipe time looked suspicious?

<p align="center"> <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/5_1_random_forest.png style="margin: 0 auto; display: block; height: 600px;"><br/> <em>Figure 5.1. Random Forest Algorithm</em> </p>

## 5.2 XGBoost

If Random Forest is the safe choice, then XGBoost is our heavy weapon. The biggest reason we insisted on including this algorithm in the article was because we learned from experienced people in the data community.

Anyone who has spent time competing in Data Science contests on Kaggle is probably familiar with XGBoost as a tool that helps push rankings higher. The community recommends it because this algorithm not only works extremely well on tabular data, but also optimizes training speed effectively. For an extremely imbalanced problem like Fraud Detection, where fraudulent transactions account for less than 1% of the dataset, XGBoost’s Gradient Boosting approach shows stronger performance. It learns continuously from the mistakes of previous decision trees, helping it catch fraudulent tricks that simpler models might easily miss.

<p align="center"> <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/5_2_xgboost.png style="margin: 0 auto; display: block; height: 500px;"><br/> <em>Figure 5.2. XGBoost Algorithm; source: <a href="https://www.researchgate.net/figure/The-Workflow-of-the-XGBoost-Algorithm-This-figure-depicts-the-step-by-step-workflow-of_fig5_383818689">ResearchGate</a></em> </p>

# 6. Model Training & Evaluation

## 6.1 Random Forest

With Random Forest, the first question we asked was: “How many trees are enough?” If we use too few trees, the model underfits. If we use too many, training becomes slower without bringing much improvement.

Instead of guessing, we used OOB Error (Out-of-Bag Error) to run an experiment. We looped from 10 to 200 trees, increasing by 20 each time, and observed at which point the error started to converge and stopped decreasing.

```python
import matplotlib.pyplot as plt
import time
oob_errors = []

# Try from 10 to 200, step 20
for i in range(10, 201, 20):
    start = time.time()
    rf = RandomForestClassifier(
        n_estimators=i,
        max_depth=10,
        warm_start=True, # Help continue training from the old number of trees
        oob_score=True,
        n_jobs=-1,
        random_state=42,
        class_weight='balanced'
    )
    rf.fit(X_train, y_train)
    oob_errors.append(1 - rf.oob_score_)
    print("="*50)
    print(f"n_estimators: {i} - OOB Error: {1 - rf.oob_score_}")
    print(f"Training time: {time.time()-start}s")

plt.plot(range(10, 201, 20), oob_errors)
plt.xlabel("n_estimators")
plt.ylabel("OOB Error Rate")
plt.title("Random Forest")
plt.show()
```

<p align="center"> <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/6_1_rf_training_loss.png style="margin: 0 auto; display: block; height: 500px;"><br/> <em>Figure 6.1. Random Forest Training Loss</em> </p>

Experimental result: Looking at the OOB Error chart, we noticed that at 10 trees, the error was still relatively high (>0.0155). Interestingly, the curve unexpectedly hit its lowest point at 50 trees. However, we decided not to choose that number. In Random Forest, sudden “bottom-outs” like that are often caused by random variance in the sampled data, and for a heavily imbalanced problem like this, a small drop in OOB error, which is based on overall accuracy, does not necessarily mean better fraud detection.

Instead, we looked at the bigger picture: from 100 trees onward, the error stayed stable around ~0.0141. Adding more trees after that, such as 150 or 200, only increased training time two or three times without making the model noticeably smarter. Therefore, our team chose n_estimators = 100 as the sweet spot, balancing speed and stability.

However, even though the training process was smooth, when we evaluated it on the Test set, Random Forest revealed a critical weakness: Precision dropped dramatically to just 0.08, meaning it produced too many false alarms.

## 6.2 XGBoost

Unlike Random Forest, which builds independent trees, XGBoost learns sequentially: each new tree corrects the mistakes of the previous one. Because of that, tree depth (max_depth) is an important parameter that determines whether the model can capture subtle fraud patterns.

Our team ran experiments on the raw data with three max_depth values: 5, 7, and 10. The results on the Test set were:

max_depth = 5: Training time was 101s. The model achieved Recall = 0.83.
max_depth = 7: Training time was 111s. Recall increased slightly to 0.84.
max_depth = 10: Training time was 125s. Recall reached 0.85, while Precision also climbed to 0.95, pushing the F1-score to 0.90.

**Checking XGBoost convergence:**
Similar to Random Forest, we also needed to determine how many trees XGBoost required. By feeding both the Train and Validation sets into eval_set to monitor Logloss, we plotted the learning curve.

```python
import matplotlib.pyplot as plt
import time
from xgboost import XGBClassifier

start = time.time()

model = XGBClassifier(
    n_estimators=200,
    learning_rate=0.1,
    max_depth=10,
    random_state=42,
    device='cuda',
    eval_metric='logloss'
)

# Train mô hình (XGBoost tự lưu logloss của từng epoch vào kết quả)
model.fit(
    X_train, y_train,
    eval_set=[(X_train, y_train), (X_val, y_val)], # Monitor train and val to see if overfitting.
    verbose=10 # Print every 20
)

training_time = time.time() - start
print(f"Training Time: {training_time:.2f}s")


results = model.evals_result()
epochs = range(1, len(results['validation_0']['logloss']) + 1)

plt.figure(figsize=(10, 6))
plt.plot(epochs, results['validation_0']['logloss'], label='Train Error')
plt.plot(epochs, results['validation_1']['logloss'], label='Val Error')

plt.xlabel("n_estimators (Epochs)")
plt.ylabel("Logloss")
plt.title("XGBoost Convergence - Logloss Over Epochs")
plt.legend()
plt.grid(True)
plt.show()
``` 

<p align="center"> <img src=https://github.com/EvelynMyNguyen210/Warmup02_Debug_Team/blob/main/Collection_Blog3/6_2_xgboost_training_loss.png style="margin: 0 auto; display: block; height: 500px;"><br/> <em>Figure 6.2. XGBoost Training Loss</em> </p>

Looking at the convergence chart above, you can clearly see the training process through two curves: Train Error (blue) and Val Error (orange).

Rapid learning phase (0 - 50 trees): The Val Error drops sharply. This is when the model is learning the most obvious fraud patterns.
Refinement phase (50 - 100 trees): Val Error continues to decrease, but more slowly, as the model starts learning more complex fraud techniques.
Convergence and overfitting risk phase (100 - 150 trees and beyond): As we approach 150 trees, the Val Error becomes almost flat, signaling that the model has reached its predictive limit on unseen data.

If we continue training to 200 rounds, you will see that the Train Error (blue) keeps dropping, while the Val Error barely changes. That means the model is starting to memorize the training set, which brings no real world benefit for prediction and only wastes computing resources.

## 6.3 Evaluation on Raw Data

To highlight the importance of data preprocessing and feature engineering, I compared the two models directly on the raw dataset, meaning the data had not been cleaned or transformed, except for encoding categorical columns into numbers while keeping the original imbalance.

1. Random Forest results on Raw Data

Because the dataset is extremely imbalanced, I had to use class_weight='balanced' to force Random Forest to pay more attention to fraudulent transactions. Here are the results:

```
            --- RANDOM FOREST - RAW DATA ---
              precision    recall  f1-score   support

           0     1.0000    0.9859    0.9929   1270881
           1     0.0835    0.9903    0.1540      1643

    accuracy                         0.9860   1272524
   macro avg     0.5417    0.9881    0.5735   1272524
weighted avg     0.9988    0.9860    0.9918   1272524
```

At first glance, the Recall score (0.99) makes it seem like Random Forest caught 99% of the fraud cases. But look at Precision (0.08), a disastrous number that pulls the F1-score down to just 0.15. This means the model is aggressively catching almost everything, including many legitimate transactions. For every 100 fraud alerts, about 92 are actually innocent customers being wrongly blocked. In a dataset with heavy noise and severe imbalance, Random Forest seems to struggle to separate the two classes, which leads to unstable and unreliable overall performance.

2. XGBoost results on Raw Data

In contrast, XGBoost shows why it is so popular in the research community and in competitions like Kaggle. Even when working with raw data that has not been carefully processed, Gradient Boosting is still able to extract and learn hidden patterns.

Its step-by-step learning process, where weak learners are combined to correct one another’s errors, helps XGBoost gradually improve performance and capture more complex fraud cases. As a result, the model not only maintains a strong fraud detection rate, but also keeps false positives under better control, achieving a much better balance across evaluation metrics.

```
                --- XGBOOST - RAW DATA ---
              precision    recall  f1-score   support

           0       1.00      1.00      1.00   1270881
           1       0.95      0.85      0.90      1643

    accuracy                           1.00   1272524
   macro avg       0.98      0.92      0.95   1272524
weighted avg       1.00      1.00      1.00   1272524
```

The result shows that XGBoost almost automatically adapts well to this challenging imbalance problem. With Precision at 0.95 and Recall at 0.85, the model detects most fraudulent transactions while keeping the false alarm rate very low.

The balance between these two metrics reflects much stronger class separation ability compared with Random Forest under the same conditions. At this stage, XGBoost clearly outperforms and dominates in overall performance.

## 6.4 Evaluation on Processed Data

Although XGBoost performed impressively on the raw data, one fundamental principle in Data Science always holds true: “Garbage in, garbage out.” The quality of the input data has a decisive impact on model performance.

That is why I retrained both models on the cleaned and standardized dataset, which was also enriched with new features through Feature Engineering, such as balance-difference variables (errorOrig, errorDest) that carry highly informative signals for classification.

The results clearly show the impact of this step: once the input data improved, the models not only learned better, but also exploited the hidden signals more effectively. What is especially interesting is that the performance gap between the two models began to change.

1. XGBoost results on Processed Data

```
                --- XGBOOST - PROCESSED DATA ---
              precision    recall  f1-score   support

           0       1.00      1.00      1.00    552439
           1       0.89      0.99      0.94      1643

    accuracy                           1.00    554082
   macro avg       0.94      1.00      0.97    554082
weighted avg       1.00      1.00      1.00    554082
```

As expected, when trained on the cleaned dataset with informative engineered features, XGBoost improved significantly. Recall increased from 0.85 to 0.99, while Precision remained at 0.89, which is still acceptable. This shows that the model not only detects fraudulent transactions effectively, but also keeps false alerts at a reasonable level. This is a very convincing result and is entirely feasible for real-world deployment.

However, the story does not end here. The result of Random Forest in the next section is the one that brings the real surprise.

2. Random Forest results on Processed Data

```
                --- RANDOM FOREST - PROCESSED DATA ---
             precision    recall  f1-score   support

           0     1.0000    1.0000    1.0000    552439
           1     0.9933    0.9970    0.9951      1643

    accuracy                         1.0000    554082
   macro avg     0.9967    0.9985    0.9976    554082
weighted avg     1.0000    1.0000    1.0000    554082
```

This result shows a remarkable improvement. Random Forest moved from a severely imbalanced state, where Precision was only 0.08, to nearly optimal performance across all three metrics. Notably, the model achieved Precision of 0.9933 and Recall of 0.9970 for the fraud class, showing that it could detect nearly all fraudulent cases while minimizing false alerts to the greatest extent possible.

This dramatic improvement can be explained by how well the model fits the data’s feature structure. In the original dataset, the boundary between the two classes was not clearly expressed. In that case, models like XGBoost can perform well because boosting allows each tree to gradually correct errors and strengthen predictive power.

However, after Feature Engineering, new variables such as errorOrig and errorDest made the difference between legitimate and fraudulent transactions much more obvious. Random Forest, which is built on decision trees that rely on binary splits, can directly exploit strongly separating features. As a result, the model learns the classification boundary in a direct and effective way without needing more complex optimization mechanisms.

From these experiments, we can draw several important conclusions. First, strong models like XGBoost can still perform well even when the data has not been perfectly processed. Second, good Feature Engineering can dramatically change the outcome and allow simpler models like Random Forest to reach outstanding performance.

In other words, choosing the right algorithm is important, but the quality of data processing is the factor that ultimately defines the model’s performance ceiling.

# 7. Limitations and Future Development Directions

Although the focus on data processing and feature building has significantly improved model performance, the project still faces certain technical hurdles that need to be optimized in the future.

## 7.1. Limitations

**Synthetic Data Bias.** Although PaySim is a high-quality simulated dataset, it still relies on fixed rules. In reality, human fraud behavior is more flexible and unpredictable than automated data generation algorithms, meaning that the generated features may not fully cover all real-world scenarios.

**The scarcity of useful characteristics (Feature Scarcity.** In the original dataset of PaySim, there are a very limited number of columns. In particular, the most important columns are such as `oldbalanceOrg`, `newbalanceOrig`, `oldbalanceDest`, and `newbalanceDest`. These columns often contain direct answers to transactions, such as fraudulent transactions typically draining accounts to zero. Removing or limiting the use of these columns to avoid data leakage leaves too few variables, making it difficult to build a comprehensive model.

**Missing identifying and contextual information (Contextual Data Gap).** The dataset lacks crucial information such as geographical location (IP/GPS), device ID, login history, or the frequency of past transactions by the user. Without this contextual information, relying solely on the amount and type of transaction makes it extremely difficult to distinguish between a large, legitimate transaction and a fraudulent one.

**Reliance on domain expertise.** The techniques employed in this project are primarily based on statistical observations. However, to detect complex forms of money laundering or fraud via social media, the system requires a deeper integration of data and expertise from financial/legal professionals.

## 7.2. Future Improvements

To enhance the value of the data processing workflow, the following areas of improvement will be prioritized:

**Automated Feature Engineering.** Apply tools such asFeaturetoolsor Deep Learning algorithmsAutoencoders) to automatically extract latent features that the human eye or conventional statistical analysis might miss.

**Entity and Relationship Analysis (Graph Features).** Instead of treating each transaction as independent, the future direction will build characteristics based on graphs. Calculating metrics such as node degree or centrality between accounts will help identify organized crime networks.

**Feature Selection Optimization.** Using advanced techniques like SHAP (SHapley Additive exPlanations) or Boruta to remove confounding features, retaining only variables that truly contribute to the model, thereby making the model more streamlined and faster.

**Data/Concept Drift Processing.** Develop a process to monitor changes in data distribution over time. When features no longer retain their original categorical value, the system will automatically trigger a feature reengineering process to adapt to new fraud schemes.

# 8. Conclusion

The project demonstrated that in fraud detection, the features’ quality is just as important as the algorithms themselves. Through the EDA process, we found that fraudulent transactions typically fall into two categories: `TRANSFER` and `CASH_OUT`. And focusing on exploiting the relationships between the few remaining variables helped optimize the model's classification capabilities.

The comparison results show that the model trained on the processed dataset and with feature extraction (Feature Engineering) has superior accuracy (F1-Score) compared to using raw data. The creation of variables such as `isErrorOrig` and `isErrorDest`, has helped the model capture anomalies that individual variables cannot reveal.

A data-centric approach helps businesses better understand customer behavior instead of treating machine learning models as a black box. This allows banks to build rule-based systems combined with AI in a transparent manner, thereby minimizing financial risks and protecting brand reputation.

# References
Lopez-Rojas, E., & Axelsson, S. (2014). PaySim: A financial mobile money simulator for fraud detection.

Kaggle. (n.d.). PaySim dataset. Retrieved from https://www.kaggle.com/datasets/ealaxi/paysim1/data


