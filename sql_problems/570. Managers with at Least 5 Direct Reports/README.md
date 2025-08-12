# Find Managers with ≥5 Direct Reports

This document explains the SQL query used to find managers who have at least **5 direct reports**.

## Query

```sql
SELECT name 
FROM Employee 
WHERE id IN (
  SELECT managerId 
  FROM Employee
  GROUP BY managerId
  HAVING COUNT(id) >= 5
);
```

---

## Table Structure (assumed)

| Column    | Description                                      |
| --------- | ------------------------------------------------ |
| id        | Employee ID (Primary Key)                        |
| name      | Employee name                                    |
| managerId | ID of the employee’s manager (Foreign Key to id) |

---

## Example Data

| id | name    | managerId |
| -- | ------- | --------- |
| 1  | Alice   | NULL      |
| 2  | Bob     | 1         |
| 3  | Carol   | 1         |
| 4  | David   | 1         |
| 5  | Eve     | 1         |
| 6  | Frank   | 1         |
| 7  | Grace   | 2         |
| 8  | Heidi   | 2         |
| 9  | Ivan    | 2         |
| 10 | Judy    | 2         |
| 11 | Mallory | 2         |
| 12 | Oscar   | 3         |

---

## Step-by-Step Execution

### 1. Subquery Execution

```sql
SELECT managerId,count(id) as empt_id
FROM Employee
GROUP BY managerId
HAVING COUNT(id) >= 5;
```

Result:

| managerId | emp_count |
|-----------|-----------|
| 1         | 5         |
| 2         | 5         |


### 2. Outer Query Execution

```sql
SELECT name 
FROM Employee 
WHERE id IN (1, 2);
```

Result:

| name  |
| ----- |
| Alice |
| Bob   |

---

## Final Output

```
Alice
Bob
```

---

## Notes

* `GROUP BY managerId` groups employees by their manager.
* `HAVING COUNT(id) >= 5` filters managers with at least 5 direct reports.
* `WHERE id IN (...)` fetches the names of those managers.
* `COUNT(*)` can also be used instead of `COUNT(id)` for clarity.

---

## Improved Version (with NULL check)

```sql
SELECT name
FROM Employee
WHERE id IN (
  SELECT managerId
  FROM Employee
  WHERE managerId IS NOT NULL
  GROUP BY managerId
  HAVING COUNT(*) >= 5
);
```
