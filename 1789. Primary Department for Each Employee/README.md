# Employees with Primary Department or Single 'N' (explained like you’re 10)

## The query

```sql
SELECT employee_id, department_id
FROM Employee 
WHERE primary_flag = 'Y'
   OR employee_id IN (
       SELECT employee_id
       FROM Employee
       GROUP BY employee_id
       HAVING SUM(primary_flag = 'N') = 1
   );
```

**In one sentence:** Show employees’ department info if they **have a primary department** OR **exactly one 'N' record**.

---

## Tiny story

Think of each employee as having **stickers** for the departments they belong to. Each sticker is marked `'Y'` (primary) or `'N'` (not primary). We want:

1. Anyone with a `'Y'` sticker.
2. Anyone with exactly **one** `'N'` sticker.

---

## Sample data we’ll use

| employee\_id | department\_id | primary\_flag |
| ------------ | -------------- | ------------- |
| 1            | 101            | Y             |
| 1            | 102            | N             |
| 2            | 101            | N             |
| 3            | 103            | N             |
| 3            | 104            | N             |

---

## Step 1 — First condition (`primary_flag = 'Y'`)

From the table:

* Employee 1, dept 101 has 'Y'.
* Keep this row.

---

## Step 2 — The subquery (`IN (...)`)

```sql
SELECT employee_id
FROM Employee
GROUP BY employee_id
HAVING SUM(primary_flag = 'N') = 1;
```

**What it does:**

1. `GROUP BY employee_id` → put all records for the same employee together.
2. `SUM(primary_flag = 'N')` → count how many rows have `'N'` for each employee (in MySQL, `TRUE` = 1, `FALSE` = 0, so summing counts the trues).
3. `= 1` → only keep employees with exactly one `'N'`.

From our data:

| employee\_id | N\_count |
| ------------ | -------- |
| 1            | 1 ✅      |
| 2            | 1 ✅      |
| 3            | 2 ❌      |

So the subquery result is:

| employee\_id |
| ------------ |
| 1            |
| 2            |

---

## Step 3 — `OR employee_id IN (...)`

We take all rows where `employee_id` is in the subquery list. That means:

* Employee 1 → ✅ (in list)
* Employee 2 → ✅ (in list)
* Employee 3 → ❌

---

## Step 4 — Combine with the first condition

We return rows that match **either**:

* `'Y'` primary flag.
* Exactly one `'N'` flag.

---

## Step 5 — Final output

From our data:

| employee\_id | department\_id |
| ------------ | -------------- |
| 1            | 101            |
| 1            | 102            |
| 2            | 101            |

---

## Quick recap (kid-friendly!)

1. Look for people with a `'Y'` tag.
2. Or people with **only one** `'N'` tag.
3. Show all their department rows.
