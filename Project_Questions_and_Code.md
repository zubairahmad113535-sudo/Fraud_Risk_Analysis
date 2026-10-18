# Credit Card Fraud Detection: Questions and Code

Dataset: `credit_card_fraud_detection_2026.csv`

---

## Part 1: Setup

**Q1. Import all the required libraries.**
```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
```

**Q2. Import the dataset.**
```python
df = pd.read_csv("credit_card_fraud_detection_2026.csv")
```

**Q3. Show the first five rows.**
```python
df.head()
```

**Q4. Check the shape, columns and summary of the data.**
```python
df.shape
df.columns
df.describe()
```

**Q5. Check the data types.**
```python
df.dtypes
```

---

## Part 2: Data Cleaning

**Q6. Check the null values in every column.**
```python
df.isnull().sum()
```

**Q7. Show the percentage of null values in each column.**
```python
df.isnull().mean().mul(100).sort_values(ascending=False)
```

**Q8. Count the null values and value counts in the browser column.**
```python
df["browser"].isnull().sum()
df["browser"].value_counts(dropna=False)
```

**Q9. Is the missing browser value related to fraud?**
```python
df.groupby(df["browser"].isnull())["is_fraud"].mean() * 100
df.groupby("browser", dropna=False)["is_fraud"].mean().mul(100).sort_values(ascending=False)
```

**Q10. Check for duplicate rows and duplicate transaction IDs.**
```python
df.duplicated().sum()
df["transaction_id"].duplicated().sum()
```

**Q11. Convert transaction_datetime from text to datetime.**
```python
df["transaction_datetime"] = pd.to_datetime(df["transaction_datetime"])
df["transaction_datetime"].dtype
```

---

## Part 3: Fraud Overview

**Q12. How many transactions are fraud, and what is the percentage?**
```python
df["is_fraud"].value_counts()
df["is_fraud"].value_counts(normalize=True).mul(100)
```

**Q13. What is the fraud rate by transaction channel?**
```python
df["transaction_channel"].value_counts()
df.groupby("transaction_channel")["is_fraud"].mean().mul(100).sort_values(ascending=False)
```

**Q14. Which merchant categories have the highest fraud rate?**
```python
merchant_analysis = df.groupby("merchant_category").agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
merchant_analysis["fraud_rate"] = (merchant_analysis["fraud_rate"] * 100).round(2)
merchant_analysis.sort_values("fraud_rate", ascending=False)
```

**Q15. At what hour of the day is fraud highest?**
```python
hourly_analysis = df.groupby("transaction_hour").agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
hourly_analysis["fraud_rate"] = (hourly_analysis["fraud_rate"] * 100).round(2)
hourly_analysis.sort_values("fraud_rate", ascending=False)
```

---

## Part 4: Location Risk

**Q16. Are international transactions more likely to be fraud?**
```python
df["international_transaction"].value_counts()
df.groupby("international_transaction")["is_fraud"].mean().mul(100)
```

**Q17. What is the fraud rate for high-risk countries?**
```python
df["high_risk_country"].value_counts()
df.groupby("high_risk_country")["is_fraud"].mean().mul(100).sort_values(ascending=False)
pd.crosstab(df["high_risk_country"], df["is_fraud"], margins=True)
```

**Q18. What is the fraud rate for international transactions from high-risk countries?**
```python
combined_geo = df[(df["international_transaction"] == 1) & (df["high_risk_country"] == 1)]
combined_geo["is_fraud"].agg(["count", "sum", "mean"])
```

**Q19. What share of high-risk-country transactions are international, and the other way round?**
```python
df[df["high_risk_country"] == 1]["international_transaction"].value_counts()
df[df["international_transaction"] == 1]["high_risk_country"].value_counts()
```

**Q20. Among international transactions, how does fraud differ between high-risk and other countries?**
```python
df[df["international_transaction"] == 1].groupby("high_risk_country")["is_fraud"].agg(["count", "sum", "mean"])
```

**Q21. Does distance from home matter?**
```python
df["distance_from_home_km"].describe()
df["distance_home_group"] = pd.cut(
    df["distance_from_home_km"],
    bins=[-1, 50, 500, 2000, 5000, 10000, float("inf")],
    labels=["0–50", "51–500", "501–2000", "2001–5000", "5001–10000", "10000+"]
)
df.groupby("distance_home_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q22. Does distance from the last transaction matter?**
```python
df["distance_last_tx_group"] = pd.cut(
    df["distance_from_last_tx_km"],
    bins=[-1, 10, 50, 200, 1000, float("inf")],
    labels=["0–10", "11–50", "51–200", "201–1000", "1000+"]
)
df.groupby("distance_last_tx_group", observed=True)["is_fraud"].mean().mul(100)
```

---

## Part 5: Amount and Activity

**Q23. Does an amount far above the customer's average signal fraud?**
```python
df.groupby("is_fraud")["amount_vs_avg_ratio"].describe()
df["amount_ratio_group"] = pd.cut(
    df["amount_vs_avg_ratio"],
    bins=[-float("inf"), 1, 2, 5, float("inf")],
    labels=["≤1", "1–2", "2–5", ">5"]
)
df.groupby("amount_ratio_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q24. Does the number of transactions in the last hour matter?**
```python
df["transactions_1h_group"] = pd.cut(
    df["transactions_last_1h"],
    bins=[-1, 0, 1, 2, float("inf")],
    labels=["0", "1", "2", "3+"]
)
df.groupby("transactions_1h_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q25. Does the number of transactions in the last 24 hours matter?**
```python
df["transactions_24h_group"] = pd.cut(
    df["transactions_last_24h"],
    bins=[-1, 1, 2, 4, 7, float("inf")],
    labels=["0–1", "2", "3–4", "5–7", "8+"]
)
df.groupby("transactions_24h_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q26. Does the amount spent in the last hour matter?**
```python
df["amount_1h_group"] = pd.cut(
    df["amount_last_1h"],
    bins=[-1, 0, 100, 300, 1000, float("inf")],
    labels=["0", "1–100", "101–300", "301–1000", "1000+"]
)
df.groupby("amount_1h_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q27. Does the amount spent in the last 24 hours matter?**
```python
df["amount_24h_group"] = pd.cut(
    df["amount_last_24h"],
    bins=[-1, 100, 250, 500, 1000, float("inf")],
    labels=["0–100", "101–250", "251–500", "501–1000", "1000+"]
)
df.groupby("amount_24h_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q28. Do the days since the last transaction matter?**
```python
df["days_since_last_tx_group"] = pd.cut(
    df["days_since_last_transaction"],
    bins=[-1, 1, 3, 7, 14, float("inf")],
    labels=["0–1", "1–3", "3–7", "7–14", "14+"]
)
df.groupby("days_since_last_tx_group", observed=True)["is_fraud"].mean().mul(100)
```

**Q29. How does the transaction amount differ between fraud and genuine transactions?**
```python
df.groupby("is_fraud")["transaction_amount"].describe().round(2)
```

---

## Part 6: Card and Customer

**Q30. Are transactions where the card was not present riskier?**
```python
card_present_analysis = df.groupby("card_present").agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
card_present_analysis["fraud_rate"] = (card_present_analysis["fraud_rate"] * 100).round(2)
card_present_analysis
```

**Q31. Are transactions where the chip was not used riskier?**
```python
chip_analysis = df.groupby("used_chip").agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
chip_analysis["fraud_rate"] = (chip_analysis["fraud_rate"] * 100).round(2)
chip_analysis
```

**Q32. Are transactions where the PIN was not used riskier?**
```python
pin_analysis = df.groupby("used_pin").agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
pin_analysis["fraud_rate"] = (pin_analysis["fraud_rate"] * 100).round(2)
pin_analysis
```

**Q33. Do transactions that use a high share of the credit limit have more fraud?**
```python
df["amount_credit_limit_ratio"] = df["transaction_amount"] / df["credit_limit"]

bins = [-float("inf"), 0.01, 0.05, 0.10, 0.25, 0.50, 1, float("inf")]
labels = ["Up to 1%", "1–5%", "5–10%", "10–25%", "25–50%", "50–100%", "Above 100%"]
df["credit_limit_usage_group"] = pd.cut(df["amount_credit_limit_ratio"], bins=bins, labels=labels)

credit_limit_analysis = df.groupby("credit_limit_usage_group", observed=False).agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
credit_limit_analysis["fraud_rate"] = (credit_limit_analysis["fraud_rate"] * 100).round(2)
credit_limit_analysis
```

**Q34. Does fraud risk differ across credit score ranges?**
```python
df.groupby("is_fraud")["credit_score"].describe().round(2)

df["credit_score_group"] = pd.cut(
    df["credit_score"],
    bins=[-float("inf"), 500, 600, 700, 800, float("inf")],
    labels=["Below 500", "500–600", "600–700", "700–800", "Above 800"]
)
credit_score_analysis = df.groupby("credit_score_group", observed=False).agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
credit_score_analysis["fraud_rate"] = (credit_score_analysis["fraud_rate"] * 100).round(2)
credit_score_analysis
```

**Q35. Does a higher number of declined transactions mean higher fraud risk?**
```python
df["declined_tx_group"] = pd.cut(
    df["num_declined_last_30d"],
    bins=[-1, 0, 1, 3, 5, float("inf")],
    labels=["0", "1", "2–3", "4–5", "6+"]
)
declined_analysis = df.groupby("declined_tx_group", observed=False).agg(
    transaction_count=("is_fraud", "size"),
    fraud_cases=("is_fraud", "sum"),
    fraud_rate=("is_fraud", "mean")
)
declined_analysis["fraud_rate"] = (declined_analysis["fraud_rate"] * 100).round(2)
declined_analysis
```

---

## Part 7: Combining Fraud Signals

**Q36. Which combination of signals is best at finding fraud?**
```python
risk_combinations = {
    "High-risk country + 3+ transactions/hour": (
        (df["high_risk_country"] == 1) & (df["transactions_last_1h"] >= 3)
    ),
    "High-risk country + amount >5x average": (
        (df["high_risk_country"] == 1) & (df["amount_vs_avg_ratio"] > 5)
    ),
    "Card not present + international": (
        (df["card_present"] == 0) & (df["international_transaction"] == 1)
    )
}

total_fraud_cases = df["is_fraud"].sum()
results = []

for combination, condition in risk_combinations.items():
    flagged = df.loc[condition]
    transaction_count = len(flagged)
    fraud_cases = flagged["is_fraud"].sum()
    fraud_rate = fraud_cases / transaction_count * 100 if transaction_count > 0 else 0
    fraud_capture_rate = fraud_cases / total_fraud_cases * 100 if total_fraud_cases > 0 else 0
    percentage_flagged = transaction_count / len(df) * 100

    results.append({
        "Combination": combination,
        "Transactions_Flagged": transaction_count,
        "Fraud_Cases": fraud_cases,
        "Fraud_Rate_%": round(fraud_rate, 2),
        "All_Fraud_Captured_%": round(fraud_capture_rate, 2),
        "Transactions_Flagged_%": round(percentage_flagged, 2)
    })

combination_analysis = pd.DataFrame(results)
combination_analysis
```

**Q37. Are high-risk-country transactions with 3 or more transactions in the last hour more likely to be fraud?**
```python
combined = df[(df["high_risk_country"] == 1) & (df["transactions_last_1h"] >= 3)]
combined["is_fraud"].agg(["count", "sum", "mean"])
```

**Q38. Are high-risk-country transactions with an amount above 5x the average more likely to be fraud?**
```python
combined_amount = df[(df["high_risk_country"] == 1) & (df["amount_vs_avg_ratio"] > 5)]
combined_amount["is_fraud"].agg(["count", "sum", "mean"])
```

---

## Part 8: Export for Power BI

**Q39. Save the prepared data as a CSV file for Power BI.**
```python
df.to_csv("fraud_detection_powerbi.csv", index=False)
```

**Q40. Check where the file was saved.**
```python
import os
os.getcwd()
```
