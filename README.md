# Assessment_Q3_sql

## Account Inactivity Alert

The operations team of the financial institution is looking to identify and flag customer accounts, whether savings or investment accounts, that have not received any deposits or inflow transactions in over one year. By flagging these inactive accounts, the team aims to detect possible signs of customer disengagement or account dormancy. This will enable to take appropriate action, such as reaching out to the customer, offering incentives to resume activity, or conducting internal audits on account performance.

---
## Objectives 

The primary objective of this project is to:

- Identify all active savings and investment accounts that have had no transactions or charges in the last 365 days.

- Calculate the number of inactivity days for each account based on the most recent transaction date.

- Categorize the account type (Savings or Investment) to allow tailored follow-up actions.

- Provide a clear output with essential details such as plan ID, owner ID, last transaction date, type of account, and number of inactive days.
---

## Complete Query
```
SELECT 
    s.savings_id AS plan_id,
    s.owner_id,
    'Savings' AS type,
    MAX(s.transaction_date) AS last_transaction_date,
    DATEDIFF(CURDATE(), MAX(s.transaction_date)) AS inactivity_days
FROM savings_savingsaccount s
GROUP BY s.savings_id, s.owner_id
HAVING MAX(s.transaction_date) IS NOT NULL
   AND DATEDIFF(CURDATE(), MAX(s.transaction_date)) > 365
```
UNION
```
SELECT 
    p.id AS plan_id,
    p.owner_id,
    'Investment' AS type,
    p.last_charge_date AS last_transaction_date,
    DATEDIFF(CURDATE(), p.last_charge_date) AS inactivity_days
FROM plans_plan p
WHERE p.last_charge_date IS NOT NULL
  AND DATEDIFF(CURDATE(), p.last_charge_date) > 365;
```
---
## My Approach

I select the savings account ID and rename it to plan_id so that it matches the structure of the investment part.
```
  SELECT savings_savingsaccount.savings_id AS plan_id,
```

I select the ID of the customer who owns the savings account.
```
  savings_savingsaccount.owner_id,
```

I manually add a column named 'type' that will just say "Savings" for each result. This helps label the kind of account.
```
  'Savings' AS type,
```

I use the MAX() function to get the latest transaction date for each account. Savings accounts can have multiple transactions, and I want the most recent one.
```
  MAX(savings_savingsaccount.transaction_date) AS last_transaction_date,
```

I use the DATEDIFF() function to calculate the number of days since the last transaction by subtracting the latest transaction date from today’s date (CURDATE()).
```
  DATEDIFF(CURDATE(), MAX(savings_savingsaccount.transaction_date)) AS inactivity_day
```
```
  FROM savings_savingsaccount
```

I group the data by both the savings account ID and the customer ID. This is necessary to use the MAX() function correctly so that I get one row per account.

```
  GROUP BY savings_savingsaccount.savings_id, savings_savingsaccount.owner_id
```
```
  HAVING MAX(savings_savingsaccount.transaction_date) IS NOT NULL
```

I filter out any accounts that have never had a transaction, which means there would be no date to check.

```
   AND DATEDIFF(CURDATE(), MAX(savings_savingsaccount.transaction_date)) > 365
```

I further filter the results to only include savings accounts where the last transaction happened more than 365 days ago.

```
  UNION
```

This combines the results from the savings query above with the investment query below. It stacks them into a single list as long as both sets have the same number of columns and column types.

I select the ID of each investment plan and rename it as plan_id, matching the format of the savings result.

```
SELECT 
    plans_plan.id AS plan_id,
```

I select the ID of the customer who owns the investment plan.

```
    plans_plan.owner_id,
```

I manually add a column labeled type with the value "Investment" so each result is marked accordingly.

```
    'Investment' AS type,
```

I select the date when the investment plan was last charged (when money was last added).

```
    plans_plan.last_charge_date AS last_transaction_date,
```

I calculate how many days have passed since the last charge using DATEDIFF().

```
    DATEDIFF(CURDATE(), plans_plan.last_charge_date) AS inactivity_days
```

I get this data from the plans_plan table.

```
  FROM plans_plan
```

I exclude any plans that do not have a last charge date (i.e., never had a transaction).

```
  WHERE plans_plan.last_charge_date IS NOT NULL
```

I filter the results to only show investment plans that have been inactive for more than 365 days.

```
  AND DATEDIFF(CURDATE(), plans_plan.last_charge_date) > 365;
```
---
## Challenges & Resolutions

1. Challenge: Mismatched Column Structures Across Tables

The savings_savingsaccount and plans_plan tables have different column names and data structures. For example, the savings table uses transaction_date to record inflows, while the investment table uses last_charge_date.

Resolution:
- To make the results compatible for a UNION, I manually aligned the output structure:

- Renamed columns using AS, such as savings_id AS plan_id and id AS plan_id.

- Added a fixed type column ('Savings' or 'Investment') to clearly distinguish the source of each row.

2. Challenge: Handling Multiple Transactions per Account

Savings accounts often have many transactions, making it necessary to find only the most recent transaction for each one.

Resolution:
Used the MAX(transaction_date) function in combination with GROUP BY savings_id, owner_id to retrieve only the latest transaction for each account.

3. Challenge: Filtering Out Accounts With No Transactions

Some savings accounts may not have any transactions recorded at all (i.e., transaction_date is NULL). Including these could cause inaccurate results or calculation errors.

Resolution:
Added a HAVING MAX(transaction_date) IS NOT NULL clause to exclude accounts that have never been used from the results.

```
  HAVING MAX(transaction_date) IS NOT NULL
```
4. Challenge: Calculating Inactivity Days

To determine account inactivity, I needed to calculate how many days had passed since the last inflow. This required comparing dates accurately in SQL.

Resolution:
Used the built-in DATEDIFF(CURDATE(), date_column) function to get the number of inactivity days for both savings and investment accounts.

```
  DATEDIFF(CURDATE(), MAX(transaction_date))
```

5. Challenge: Ensuring Compatibility for UNION

The SQL UNION operator requires that both subqueries return the same number and type of columns in the same order.

Resolution:

- Matched both queries to return exactly five columns.

- Aligned column names (plan_id, owner_id, type, last_transaction_date, inactivity_days).

- Ensured data types were consistent (e.g., string for type, date for transaction, integer for inactivity days
























