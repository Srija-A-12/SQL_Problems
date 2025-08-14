# Biggest number that appears **only once**

## The query

```sql
SELECT MAX(num) AS num
FROM MyNumbers
WHERE num IN (
  SELECT num
  FROM MyNumbers
  GROUP BY num
  HAVING COUNT(*) = 1
);
```

**In one sentence:** Find the **largest** number that shows up **exactly one time** in the table `MyNumbers`.

---

## Tiny story

Imagine you have a list of stickers (numbers). Some stickers show up many times, some only once. First, we keep only the stickers that show up **one time**. Then, from those, we pick the **biggest** number. That’s it!

---

## Sample data we’ll use

Think of `MyNumbers` as a one‑column table:

| num |
| --: |
|   5 |
|   7 |
|   5 |
|  12 |
|  -2 |
|  12 |
|  10 |
|  12 |

We’ll show how the table changes at each step of the query.

---

## Step 1 — Look at the **inner** query (the part inside `IN (...)`)

```sql
SELECT num
FROM MyNumbers
GROUP BY num
HAVING COUNT(*) = 1;
```

**What it does:**

1. `GROUP BY num` puts the same numbers together and lets us **count** them.
2. `HAVING COUNT(*) = 1` keeps **only** the numbers that appear **once**.

### First, count how many times each number appears

From our sample table:

| num | how\_many\_times |
| --: | ---------------: |
|  -2 |                1 |
|   5 |                2 |
|   7 |                1 |
|  10 |                1 |
|  12 |                3 |

### Then, keep only the ones with count = 1

Result of the inner query:

| num |
| --: |
|  -2 |
|   7 |
|  10 |

These are the numbers that appear **exactly once**.

---

## Step 2 — Use `WHERE num IN (...)` on the **outer** query

```sql
SELECT MAX(num) AS num
FROM MyNumbers
WHERE num IN ( ...the list [-2, 7, 10] from Step 1... );
```

**What it does:**

* From the original table, keep only the rows whose `num` is in that list.

Filtered rows:

| num |
| --: |
|  -2 |
|   7 |
|  10 |

(We tossed out `5` and `12` because they didn’t appear exactly once.)

---

## Step 3 — `SELECT MAX(num)` picks the biggest one

```sql
SELECT MAX(num) AS num
```

From `-2, 7, 10`, the biggest is **10**.

**Final answer returned by the full query:**

| num |
| --: |
|  10 |

---

## Why this works

* **`GROUP BY` + `HAVING COUNT(*) = 1`** finds the numbers that appear once.
* **`WHERE num IN (...)`** keeps only those numbers in the main table.
* **`MAX`** chooses the biggest number from that smaller set.

So the query returns: *“the largest number that appears exactly once.”*

---

## Edge cases

* If **no number** appears exactly once, the result is **`NULL`** (empty value). If you want to show `0` instead, you can wrap it:

  ```sql
  SELECT COALESCE(
    (
      SELECT MAX(num)
      FROM MyNumbers
      WHERE num IN (
        SELECT num FROM MyNumbers GROUP BY num HAVING COUNT(*) = 1
      )
    ),
    0
  ) AS num;
  ```

---

## Shorter (but same idea)

Some people like writing it this way:

```sql
SELECT MAX(num) AS num
FROM (
  SELECT num
  FROM MyNumbers
  GROUP BY num
  HAVING COUNT(*) = 1
) AS singles;
```

This makes the “only-once” list first (we called it `singles`) and then picks the max from it.

---

## Quick recap (kid‑friendly!)

1. Count how many times each number shows up.
2. Keep the numbers that show up **one time**.
3. From those, pick the **biggest**.

That’s exactly what the query does.
