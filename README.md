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









