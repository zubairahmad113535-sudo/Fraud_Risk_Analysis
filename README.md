# Fraud Detection Analysis

## 📌 Project Overview

This project focuses on analyzing **100,000 financial transactions** to identify transaction patterns that are associated with fraudulent activity.

The project uses **Python (Pandas)** for data cleaning, exploration, grouping, and risk analysis, followed by **Power BI** for interactive visualization and dashboard development.

The goal was not to build a machine learning model, but to use **descriptive and exploratory data analysis** to identify meaningful fraud-risk patterns and suggest potential triggers for additional transaction review.

---

## 🎯 Business Problem

Financial institutions process a large number of transactions every day, making it important to identify unusual transaction patterns that may require further investigation.

The main business question for this project was:

> **Which transaction patterns consistently signal fraudulent behavior, and what trigger could be used for additional review?**

---

## 📊 Dataset

The dataset contains **100,000 transaction records** with **43 columns** covering transaction, customer, card, merchant, and fraud-related information.

Some important fields include:

- Transaction amount
- Transaction channel
- Merchant category
- International transaction
- High-risk country
- Card present
- Transaction frequency
- Amount vs. average transaction amount
- Credit score
- Declined transactions
- Browser
- Device type
- Fraud indicator
- Fraud type
- Fraud amount lost

---

## 🛠️ Tools & Technologies

- **Python**
- **Pandas**
- **Jupyter Notebook**
- **Power BI**
- **DAX**
- **Data Visualization**

---

## 🔍 Analysis Performed

I analyzed several potential fraud-risk signals, including:

### 1. Transaction Channel
Compared fraud rates across Online, In-store, Mobile App, and ATM transactions.

### 2. International Transactions
Compared fraud rates between international and non-international transactions.

### 3. High-Risk Countries
Investigated whether transactions associated with high-risk countries had higher historical fraud rates.

### 4. Card Presence
Compared card-present and card-not-present transactions.

### 5. Transaction Frequency
Analyzed whether multiple transactions within a short period were associated with higher fraud rates.

### 6. Transaction Amount
Compared transaction amounts with customers' average transaction amounts.

### 7. Merchant Categories
Identified merchant categories with relatively higher historical fraud rates.

### 8. Declined Transactions
Investigated whether repeated declined transactions were associated with higher fraud rates.

### 9. Combined Risk Signals
Combined multiple indicators to identify potentially stronger fraud-risk patterns.

---

## 📈 Key Findings

The overall historical fraud rate in the dataset was approximately **2.06%**.

Some important findings included:

- International transactions had a higher fraud rate than non-international transactions.
- Card-not-present transactions had a higher fraud rate than card-present transactions.
- Transactions associated with high-risk countries had an **11.05% historical fraud rate**.
- Transactions from a high-risk country with **3 or more transactions within one hour** had a **25.60% historical fraud rate**.
- This combined condition identified **418 transactions**, of which **107 were fraudulent**.

### Combined Risk Signal

The analysis showed:

**418 flagged transactions → 107 fraud cases → 25.60% fraud rate**

This suggests that combining multiple risk signals can provide a stronger basis for identifying transactions that may require additional review.

> These findings represent historical associations in the dataset and should not be interpreted as proof that a transaction is fraudulent.

---

## 📊 Power BI Dashboard

The Power BI dashboard was created to provide an interactive view of:

- Total Transactions
- Fraudulent Transactions
- Fraud Rate
- Transaction Channels
- Merchant Categories
- International Transactions
- High-Risk Countries
- Fraud-Risk Patterns
- Key Fraud Indicators

The dashboard allows users to explore the data and identify areas with relatively higher historical fraud rates.

---

## 💡 Business Value

This analysis demonstrates how transaction data can be transformed into actionable insights.

The identified patterns could be used as **candidate rules for additional review**, such as:

> **High-risk country + 3 or more transactions within one hour**

Such rules could help prioritize transactions for further investigation while recognizing that flagged transactions are not automatically fraudulent.

---

## 📚 Key Learning

Through this project, I strengthened my practical skills in:

- Data cleaning
- Exploratory data analysis
- Pandas
- Grouping and aggregation
- Fraud-rate calculation
- Risk segmentation
- Business-question-driven analysis
- Combining multiple analytical signals
- Power BI dashboard development
- Communicating analytical findings

This project is part of my ongoing journey toward becoming a **Data Analyst**.
