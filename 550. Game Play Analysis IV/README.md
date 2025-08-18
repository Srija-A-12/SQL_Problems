

# 550. Fraction of Players Logging in the Next Day

## 🔹 Problem

We want to find the **fraction of players** who logged in **on the day immediately after their first login date**.

For example:

* If a player’s first login is on `2025-08-15`, we check if they also logged in on `2025-08-16`.
* If yes → count them.
* Finally, divide that count by the **total number of distinct players**.

---

## 🔹 Query Explanation

```sql
SELECT 
    ROUND(
        COUNT(DISTINCT player_id) /
        (
            SELECT COUNT(DISTINCT player_id)
            FROM Activity
        ), 2
    ) AS fraction
FROM Activity 
WHERE (player_id, DATE_SUB(event_date, INTERVAL 1 DAY)) IN (
    SELECT 
        player_id, 
        MIN(event_date) AS first_login
    FROM Activity  
    GROUP BY player_id 
);
```

---

### 🔸 Step-by-step breakdown

1. **Find first login date per player**

   ```sql
   SELECT player_id, MIN(event_date) AS first_login
   FROM Activity
   GROUP BY player_id;
   ```

   * Groups data by `player_id`.
   * Finds each player’s first login date.

---

2. **Check who logged in the day after first login**

   ```sql
   WHERE (player_id, DATE_SUB(event_date, INTERVAL 1 DAY)) IN (...)
   ```

   * For each row in `Activity`, subtract 1 day from `event_date`.
   * If `(player_id, event_date - 1)` matches `(player_id, first_login)`, it means that player logged in **on the next day** after their first login.

---

3. **Count distinct players who satisfied this condition**

   ```sql
   COUNT(DISTINCT player_id)
   ```

   * Ensures each qualifying player is counted once.

---

4. **Divide by total number of distinct players**

   ```sql
   (
       SELECT COUNT(DISTINCT player_id) FROM Activity
   )
   ```

   * Gets the denominator = total unique players.

---

5. **Round to 2 decimal places**

   ```sql
   ROUND(..., 2)
   ```

   * Gives a clean fraction like `0.33` instead of `0.333333`.

---

## 🔹 Example

**Activity table:**

| player\_id | event\_date |
| ---------- | ----------- |
| 1          | 2025-08-15  |
| 1          | 2025-08-16  |
| 2          | 2025-08-14  |
| 2          | 2025-08-20  |
| 3          | 2025-08-10  |

* Player 1 → first login = `2025-08-15`, logged in next day (`2025-08-16`) ✅
* Player 2 → first login = `2025-08-14`, no login on `2025-08-15` ❌
* Player 3 → first login = `2025-08-10`, no login on `2025-08-11` ❌

👉 Fraction = **1 / 3 = 0.33**

---

## 🔹 Final Output

The query returns a single column:

| fraction |
| -------- |
| 0.33     |

---


