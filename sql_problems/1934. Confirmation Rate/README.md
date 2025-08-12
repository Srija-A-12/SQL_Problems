
```markdown
# 1934. Confirmation Rate

## Problem Statement
You are given two tables: **Signups** and **Confirmations**.  
The task is to calculate the confirmation rate for each user.  
The confirmation rate is defined as:

```

confirmation\_rate = (number of 'confirmed' actions) / (total confirmations for that user)

````

If a user has no confirmation records, their confirmation rate should be `0.00`.

---

## SQL Query

```sql
SELECT 
    s.user_id, 
    CASE 
        WHEN c.time_stamp IS NULL THEN 0.00
        ELSE ROUND(SUM(c.action = 'confirmed') / COUNT(*), 2) 
    END AS confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c
    ON s.user_id = c.user_id 
GROUP BY s.user_id;
````

---

## Line-by-Line Explanation

| SQL Code Line                                           | Explanation                                                                                                                           |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `SELECT s.user_id,`                                     | Selects the unique user ID from the `Signups` table.                                                                                  |
| `CASE WHEN c.time_stamp IS NULL THEN 0.00`              | If there are no matching rows in `Confirmations`, set the confirmation rate to `0.00`.                                                |
| `ELSE ROUND(SUM(c.action = 'confirmed') / COUNT(*), 2)` | If confirmations exist, count how many have `action = 'confirmed'`, divide by the total confirmations, and round to 2 decimal places. |
| `END AS confirmation_rate`                              | Labels the calculated value as `confirmation_rate`.                                                                                   |
| `FROM Signups s`                                        | Use the `Signups` table as the starting point because we want to list all registered users.                                           |
| `LEFT JOIN Confirmations c`                             | Include all users, even if they have no confirmation records, by using `LEFT JOIN`.                                                   |
| `ON s.user_id = c.user_id`                              | Match each signup to their corresponding confirmations by `user_id`.                                                                  |
| `GROUP BY s.user_id`                                    | Group data per user so the aggregates (`SUM` and `COUNT`) work correctly.                                                             |

---

## Key Points

* **Boolean SUM**: In MySQL, `(c.action = 'confirmed')` evaluates to `1` for true, `0` for false, so `SUM(...)` counts confirmed actions.
* **LEFT JOIN** ensures users without confirmations are still included in results.
* **ROUND** formats the result to 2 decimal places.
* **CASE WHEN** handles missing data cleanly.

---

## Example

### Input Tables

**Signups**

| user\_id | time\_stamp |
| -------- | ----------- |
| 3        | 2020-03-21  |
| 7        | 2020-01-04  |
| 2        | 2020-07-29  |

**Confirmations**

| user\_id | time\_stamp | action    |
| -------- | ----------- | --------- |
| 3        | 2021-01-06  | confirmed |
| 3        | 2021-07-14  | timeout   |
| 7        | 2021-06-12  | confirmed |

### Output

| user\_id | confirmation\_rate |
| -------- | ------------------ |
| 3        | 0.50               |
| 7        | 1.00               |
| 2        | 0.00               |

---


