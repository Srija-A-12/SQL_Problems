# Employees with Low Salary and Missing Manager (explained like you’re 10)

## The query

```sql
SELECT employee_id 
FROM Employees 
WHERE salary < 30000  
  AND manager_id NOT IN (
      SELECT employee_id
      FROM Employees
  )
ORDER BY 1;
```

**In one sentence:** Show employees earning less than **30,000** whose manager is **not listed** as an employee in the table.

---

## Tiny story

Imagine a company list where every person has a boss ID. We want to find **low-salary people** whose boss **isn’t in the company list at all**.

---

## Sample data we’ll use

| employee\_id | salary | manager\_id |
| ------------ | ------ | ----------- |
| 1            | 45000  | NULL        |
| 2            | 25000  | 1           |
| 3            | 28000  | 5           |
| 4            | 50000  | 1           |
| 5            | 26000  | 10          |

---

## Step 1 — The subquery

```sql
SELECT employee_id
FROM Employees;
```

This gets the list of all employee IDs:

| employee\_id |
| ------------ |
| 1            |
| 2            |
| 3            |
| 4            |
| 5            |

---

## Step 2 — `manager_id NOT IN (...)`

We check if each employee’s `manager_id` is **not** in that list.

* Employee 2 → manager\_id = 1 → **in** list ❌
* Employee 3 → manager\_id = 5 → **in** list ❌
* Employee 5 → manager\_id = 10 → **not in** list ✅

---

## Step 3 — `salary < 30000`

Now we keep only those with salary less than 30000:

* Employee 5 → salary = 26000 ✅

---

## Step 4 — `ORDER BY 1`

Sort by `employee_id`.

---

## Final output:

| employee\_id |
| ------------ |
| 5            |

---

## Quick recap (kid-friendly!)

1. List all employee IDs.
2. Keep people whose boss’s ID is **not** in that list.
3. From those, keep only the ones earning < 30,000.
4. Sort them by ID.
