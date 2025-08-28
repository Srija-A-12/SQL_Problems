

> Always order the window by a stable sequence (typically the `id`)—otherwise results are non-deterministic.

```sql
-- MySQL 8.0+
WITH cte AS (
  SELECT
    id,
    num,
    LEAD(num, 1) OVER (ORDER BY id) AS next_1,
    LEAD(num, 2) OVER (ORDER BY id) AS next_2
  FROM Logs
)
SELECT DISTINCT num AS ConsecutiveNums
FROM cte
WHERE num = next_1 AND num = next_2;
```

---

## What are `LEAD` and `OVER`?

* `LEAD(expr, offset, default)`
  Looks **forward** within the current window and returns the value of `expr` from a **future row**.

  * `offset` = how many rows ahead (1, 2, …).
  * `default` = value returned if the future row doesn’t exist (defaults to `NULL`).

* `OVER (ORDER BY ...)`
  Defines the **window** (the ordered sequence of rows) that the function (like `LEAD`) will look at.

  * Without `ORDER BY`, the row order is undefined → your results can vary each run.
  * For this problem, we use `ORDER BY id` so “next row” truly means “next by id”.

---

## Example data

Suppose `Logs` looks like this:

| id | num |
| -- | --- |
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
| 8  | 3   |
| 9  | 3   |
| 10 | 3   |
| 11 | 3   |

We have:

* three `1`s at ids 1–3 → qualifies
* only two `2`s at ids 6–7 → not enough
* four `3`s at ids 8–11 → qualifies

---

## Step-by-step: how the query runs

### 1) Build the CTE with `LEAD`

```sql
SELECT
  id,
  num,
  LEAD(num, 1) OVER (ORDER BY id) AS next_1,
  LEAD(num, 2) OVER (ORDER BY id) AS next_2
FROM Logs;
```

Result of the CTE:

| id | num | next\_1 | next\_2 |
| -- | --- | ------- | ------- |
| 1  | 1   | 1       | 1       |
| 2  | 1   | 1       | 2       |
| 3  | 1   | 2       | 1       |
| 4  | 2   | 1       | 2       |
| 5  | 1   | 2       | 2       |
| 6  | 2   | 2       | 3       |
| 7  | 2   | 3       | 3       |
| 8  | 3   | 3       | 3       |
| 9  | 3   | 3       | 3       |
| 10 | 3   | 3       | NULL    |
| 11 | 3   | NULL    | NULL    |

*(Rows 10–11 run out of “future” rows, so `LEAD` returns `NULL`.)*

### 2) Filter rows where current and next two match

```sql
WHERE num = next_1 AND num = next_2
```

Rows that pass the filter:

* `id = 1` (1, 1, 1) ✔️
* `id = 8` (3, 3, 3) ✔️
* `id = 9` (3, 3, 3) ✔️

### 3) Deduplicate the numbers

```sql
SELECT DISTINCT num AS ConsecutiveNums
```

**Final output:**

| ConsecutiveNums |
| --------------- |
| 1               |
| 3               |

---

## Why `DISTINCT`?

Multiple starting positions inside a long run (like ids 8 and 9 in the `3`s) would otherwise repeat the same number. `DISTINCT` collapses them to unique values.

---

## Edge cases & tips

* **Must use MySQL 8.0+** for window functions (`LEAD`, `OVER`).
* **Order matters.** Replace `id` with the column that defines your true row order.
* **NULLs in `num`:** If `num` can be NULL and you want to treat NULLs as equal, you could use the null-safe equality operator:
  `WHERE num <=> next_1 AND num <=> next_2`
  (LeetCode’s table typically doesn’t have NULLs.)
* **Find 4+ in a row?** Add another `LEAD(num, 3)` and include it in the `WHERE`.

---

## Alternative (for reference)

A classic alternative is a self-join:

```sql
SELECT DISTINCT l1.num AS ConsecutiveNums
FROM Logs l1
JOIN Logs l2 ON l2.id = l1.id + 1 AND l2.num = l1.num
JOIN Logs l3 ON l3.id = l1.id + 2 AND l3.num = l1.num;
```

But the window-function solution is cleaner and scales better to “k in a row.”
