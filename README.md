

# **Credit Card Case Study**

## **Overview**
This case study analyzes credit card transaction data to uncover customer spending patterns, repayment behavior, and bank profitability. The study involves data preprocessing, statistical analysis, hypothesis testing, and data visualization.

<img width="489" alt="Screenshot 2025-02-07 at 7 38 39 PM" src="https://github.com/user-attachments/assets/5644fbe5-1434-4dc1-9d19-d9f06974b6b9" />


### **Objectives**
1. **Data Cleaning and Transformation**
   - Replace age values less than 18 with the mean age.
   - Adjust spend amounts exceeding the limit to 50% of the customer's limit.
   - Cap repayment amounts at the customer's limit.

2. **Exploratory Data Analysis**
   - Identify distinct customers and categories.
   - Compute the average monthly spend and repayment per customer.
   - Analyze profitability with an interest rate of 2.9% per month.
   - Determine top-spending product types, top-paying customers, and most profitable cities.
   - Segment spending trends based on age groups.

3. **Visualizations**
   - Yearly city-wise spending trends.
   - Monthly total spending comparison across cities.
   - Seasonal patterns in spending across products.
   - Yearly spending trend on air tickets.

4. **Hypothesis Testing**
   - Apply statistical tests to analyze customer behavior.

---

## **Data Preprocessing**
To ensure data quality, we performed the following preprocessing steps:

```python
# Replacing age < 18 with mean age
mean_age = df['age'][df['age'] >= 18].mean()
df['age'] = df['age'].apply(lambda x: mean_age if x < 18 else x)

# Adjusting spend amounts
df['spend'] = df.apply(lambda x: x['spend'] if x['spend'] <= x['limit'] else 0.5 * x['limit'], axis=1)

# Capping repayment amounts
df['repayment'] = df.apply(lambda x: x['repayment'] if x['repayment'] <= x['limit'] else x['limit'], axis=1)
```

---

## **Exploratory Data Analysis (EDA)**

### **1. Number of Distinct Customers and Categories**
```python
num_customers = df['customer_id'].nunique()
num_categories = df['category'].nunique()
print(f"Total Distinct Customers: {num_customers}, Total Categories: {num_categories}")
```

### **2. Average Monthly Spend and Repayment**
```python
df['month'] = df['transaction_date'].dt.to_period('M')

avg_monthly_spend = df.groupby('customer_id')['spend'].mean()
avg_monthly_repayment = df.groupby('customer_id')['repayment'].mean()
```

### **3. Bank Profit Calculation**
Profit is calculated using a monthly interest rate of **2.9%** applied to positive monthly balances.

```python
df['monthly_profit'] = df['repayment'] - df['spend']
df['bank_profit'] = df['monthly_profit'].apply(lambda x: x * 0.029 if x > 0 else 0)

total_profit = df.groupby('month')['bank_profit'].sum()
```

### **4. Top 5 Product Categories by Spending**
```python
top_products = df.groupby('product')['spend'].sum().nlargest(5)
```

---

## **Data Visualizations**

### **1. b. Comparison of yearly spend on air tickets**
```python

plt.bar(ats.year,ats.Amount)
plt.xlabel("year")
plt.ylabel("Amount")
plt.title("Comparison of yearly spend on air tickets")
plt.show()
```

<img width="549" alt="Screenshot 2025-02-07 at 7 41 54 PM" src="https://github.com/user-attachments/assets/f8557267-50d6-4ec4-b8a2-2b912ba7021d" />


### **2. Monthly comparison of total spends, city wise**
```python
ca_sp['monthly'] = ca_sp.Month.apply(lambda x : pd.Timestamp.strftime(x , format = '%B') )
mc = ca_sp.groupby(['City','monthly'])['Amount'].sum().reset_index()
mc.pivot(index = 'City', columns = 'monthly' , values = 'Amount').plot(kind="bar",figsize=(8,8),width=0.8)
plt.show()
```
<img width="588" alt="Screenshot 2025-02-07 at 7 41 39 PM" src="https://github.com/user-attachments/assets/17564e02-834a-4d4b-b493-6f60bc0f04a1" />


### **3. Seasonal Trends in Spending**
```python
df['month_num'] = df['transaction_date'].dt.month
seasonal_spend = df.groupby('month_num')['spend'].sum()

plt.figure(figsize=(10,5))
sns.lineplot(x=seasonal_spend.index, y=seasonal_spend.values, marker="o")
plt.xticks(range(1,13), ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"])
plt.title('Seasonal Trends in Spending')
plt.show()
```

<img width="787" alt="Screenshot 2025-02-07 at 7 40 57 PM" src="https://github.com/user-attachments/assets/096f43eb-9e8a-400d-a182-76642c61ec48" />

---

## **Hypothesis Testing**
To test if **spending behavior varies across age groups**, we use ANOVA.

```python
from scipy.stats import f_oneway

young = df[df['age'] < 30]['spend']
middle_aged = df[(df['age'] >= 30) & (df['age'] < 50)]['spend']
senior = df[df['age'] >= 50]['spend']

stat, p_value = f_oneway(young, middle_aged, senior)
print(f"ANOVA Test: Statistic = {stat}, p-value = {p_value}")

if p_value < 0.05:
    print("Significant difference in spending between age groups.")
else:
    print("No significant difference in spending between age groups.")
```

---

## **Conclusion**
This case study provided valuable insights into customer spending and repayment behaviors. The key takeaways are:
- Spending varies across cities and product categories.
- Seasonal patterns affect spending, with peaks in January and May.
- Customers below 30 tend to spend more than senior customers.
- Interest earned on positive balances contributes to the bank's profitability.

This analysis, combined with visualizations and hypothesis testing, provides a comprehensive view of credit card spending trends.

---

## **Technologies Used**
- **Python** (Pandas, NumPy, SciPy, Seaborn, Matplotlib)
- **Statistical Analysis** (Hypothesis Testing, ANOVA)
- **Data Visualization** (Bar Charts, Line Graphs)

---

