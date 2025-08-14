# Biggest number that appears **only once**

## The query

```sql
SELECT e.employee_id,
       e.name,
       COUNT(*) AS reports_count,
       ROUND(AVG(e1.age), 0) AS average_age
FROM Employees e
JOIN Employees e1
     ON e.employee_id = e1.reports_to
GROUP BY e.employee_id, e.name
ORDER BY e.employee_id;
```

**In one sentence:** For every manager, find **how many people report to them** and the **average age** of those people.

---

## Tiny story

Imagine a school where each **teacher** has some **students**. We want to find:

1. The teacher’s ID and name.
2. How many students they have.
3. The average age of those students.

---

## Sample data we’ll use

Think of `Employees` as a table:

| employee\_id | name  | age | reports\_to |
| ------------ | ----- | --- | ----------- |
| 1            | Alice | 45  | NULL        |
| 2            | Bob   | 38  | 1           |
| 3            | Carol | 29  | 1           |
| 4            | David | 33  | 2           |
| 5            | Eve   | 40  | 2           |

Here:

* `reports_to` means “this person’s manager ID.”
* `NULL` in `reports_to` means they have no manager (maybe they’re the top boss).

---

## Step 1 — `JOIN` the table to itself

```sql
FROM Employees e
JOIN Employees e1
  ON e.employee_id = e1.reports_to
```

* `e` = manager
* `e1` = their direct report
* This matches every manager with each person who reports to them.

**Result after JOIN:**

| e.employee\_id | e.name | e1.employee\_id | e1.name | e1.age | e1.reports\_to |
| -------------- | ------ | --------------- | ------- | ------ | -------------- |
| 1              | Alice  | 2               | Bob     | 38     | 1              |
| 1              | Alice  | 3               | Carol   | 29     | 1              |
| 2              | Bob    | 4               | David   | 33     | 2              |
| 2              | Bob    | 5               | Eve     | 40     | 2              |

Now each row shows `(manager, report)`.

---

## Step 2 — Count how many reports each manager has

```sql
COUNT(*) AS reports_count
```

From the JOIN result:

* Alice → 2 reports (Bob, Carol)
* Bob → 2 reports (David, Eve)

---

## Step 3 — Average the ages of those reports

```sql
AVG(e1.age)
```

From the JOIN result:

* Alice’s reports: 38, 29 → average = 33.5 → rounded to 34
* Bob’s reports: 33, 40 → average = 36.5 → rounded to 37

---

## Step 4 — `GROUP BY` manager

```sql
GROUP BY e.employee_id, e.name
```

This squashes the rows for each manager into a single row containing their ID, name, count of reports, and average age.

---

## Step 5 — Sort by manager ID

```sql
ORDER BY e.employee_id
```

So results come out in manager ID order.

---

## Final result:

| employee\_id | name  | reports\_count | average\_age |
| ------------ | ----- | -------------: | -----------: |
| 1            | Alice |              2 |           34 |
| 2            | Bob   |              2 |           37 |

---

## Quick recap (kid‑friendly!)

1. Match each manager with the people they manage.
2. Count those people.
3. Find their average age.
4. Show one row per manager.
5. Sort nicely.
