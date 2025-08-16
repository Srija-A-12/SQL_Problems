
# 📝 README — Find First Year Each Product Was Sold

We are solving this with:

```sql
with cte as (
    select *, 
           rank() over (partition by product_id order by year) as rnk
    from sales
)
select product_id, year as first_year, quantity, price
from cte
where rnk = 1;
```

---

## 1. Input Table — `sales`

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 1           | 2018 | 10       | 50    |
| 1           | 2018 | 12       | 55    |
| 1           | 2019 | 20       | 60    |
| 2           | 2020 | 15       | 40    |
| 2           | 2020 | 18       | 42    |
| 2           | 2021 | 25       | 45    |

---

## 2. Inside the CTE

```sql
rank() over (partition by product_id order by year) as rnk
```

👉 This means:

* **Partition by `product_id`** → group rows by each product.
* **Order by `year`** → order rows within each product by year.
* **Rank** → give rank `1` to the earliest year(s). If multiple rows tie for earliest year, they all get `1`.

### Resulting `cte` table:

| product\_id | year | quantity | price | rnk |
| ----------- | ---- | -------- | ----- | --- |
| 1           | 2018 | 10       | 50    | 1   |
| 1           | 2018 | 12       | 55    | 1   |
| 1           | 2019 | 20       | 60    | 3   |
| 2           | 2020 | 15       | 40    | 1   |
| 2           | 2020 | 18       | 42    | 1   |
| 2           | 2021 | 25       | 45    | 3   |

---

## 3. Filter in the Main Query

```sql
where rnk = 1
```

👉 This removes all rows **except the earliest year(s) for each product**.

---

## 4. Final Output

| product\_id | first\_year | quantity | price |
| ----------- | ----------- | -------- | ----- |
| 1           | 2018        | 10       | 50    |
| 1           | 2018        | 12       | 55    |
| 2           | 2020        | 15       | 40    |
| 2           | 2020        | 18       | 42    |

---

## ✅ Explanation in Simple Words

1. Group sales by product.
2. Rank the rows so that the earliest year gets `1`.
3. Keep only those rows.
4. Done → we now know when each product was first sold.

---

# ** 📝  Explanation for Rank,Over,partition, order**

🙌 Let’s carefully break down what’s happening with

```sql
rank() over(partition by product_id order by year)
```

This is a **window function** in SQL.
I’ll explain each keyword **step by step** with simple examples 👇

---

## 🔹 1. `over (...)`

The `over` clause tells SQL:
👉 *“Apply this function not on the entire table, but in smaller windows (groups) that I define.”*

So `over(...)` defines **how the ranking is applied** → across all rows, or partitioned (grouped) somehow.

---

## 🔹 2. `partition by`

`partition by` means:
👉 *“Break the data into groups based on this column, and run the window function separately for each group.”*

Example:
If we do

```sql
partition by product_id
```

then all rows of `product_id = 1` are one group, all rows of `product_id = 2` are another group, etc.
The ranking restarts **inside each partition**.

---

## 🔹 3. `order by`

Inside each partition (group), we need an order to assign the ranks.
Example:

```sql
order by year
```

means: sort rows of the product by year, then give ranks in that order.

---

## 🔹 4. `rank()`

Now comes the function itself:

* `rank()` assigns a **rank number** to each row based on the order.
* **If two rows tie (same value in `order by` column), they get the same rank**.
* But then the next rank is skipped.

---

### 📊 Example Table

| product\_id | year | quantity |
| ----------- | ---- | -------- |
| 1           | 2018 | 10       |
| 1           | 2018 | 12       |
| 1           | 2019 | 20       |
| 2           | 2020 | 15       |
| 2           | 2020 | 18       |
| 2           | 2021 | 25       |

---

### 📊 Apply `rank() over(partition by product_id order by year)`

| product\_id | year | quantity | rank |                         |
| ----------- | ---- | -------- | ---- | ----------------------- |
| 1           | 2018 | 10       | 1    |                         |
| 1           | 2018 | 12       | 1    | ← same year → same rank |
| 1           | 2019 | 20       | 3    | ← skipped rank 2        |
| 2           | 2020 | 15       | 1    |                         |
| 2           | 2020 | 18       | 1    |                         |
| 2           | 2021 | 25       | 3    |                         |

---

## 🔹 Difference from `row_number()` and `dense_rank()`

* `row_number()` → gives **unique increasing numbers**, even for ties.
* `rank()` → gives **same rank for ties, but skips the next number**.
* `dense_rank()` → gives **same rank for ties, but does NOT skip the next number**.

👉 Example for `product_id = 1`:

| year | row\_number | rank | dense\_rank |
| ---- | ----------- | ---- | ----------- |
| 2018 | 1           | 1    | 1           |
| 2018 | 2           | 1    | 1           |
| 2019 | 3           | 3    | 2           |

---

✅ So in your query:

```sql
rank() over(partition by product_id order by year)
```

means:
“For each product, assign a rank starting at the **earliest year**. If multiple sales happen in the same earliest year, give them the same rank.”

---

# 🤩 Let’s **visualize step by step** what happens with

```sql
rank() over (partition by product_id order by year)
```

We’ll build the intermediate tables for each keyword so you see exactly **what’s going on under the hood**.

---

### 🏁 Input Table (`sales`)

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 1           | 2018 | 10       | 50    |
| 1           | 2018 | 12       | 55    |
| 1           | 2019 | 20       | 60    |
| 2           | 2020 | 15       | 40    |
| 2           | 2020 | 18       | 42    |
| 2           | 2021 | 25       | 45    |

---

## 🔹 Step 1: `partition by product_id`

👉 Breaks the table into groups based on `product_id`.

**Partition for product 1:**

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 1           | 2018 | 10       | 50    |
| 1           | 2018 | 12       | 55    |
| 1           | 2019 | 20       | 60    |

**Partition for product 2:**

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 2           | 2020 | 15       | 40    |
| 2           | 2020 | 18       | 42    |
| 2           | 2021 | 25       | 45    |

---

## 🔹 Step 2: `order by year`

👉 Within each partition, sort rows by `year` (ascending).

**Partition for product 1 (already sorted):**

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 1           | 2018 | 10       | 50    |
| 1           | 2018 | 12       | 55    |
| 1           | 2019 | 20       | 60    |

**Partition for product 2 (already sorted):**

| product\_id | year | quantity | price |
| ----------- | ---- | -------- | ----- |
| 2           | 2020 | 15       | 40    |
| 2           | 2020 | 18       | 42    |
| 2           | 2021 | 25       | 45    |

---

## 🔹 Step 3: Apply `rank()`

👉 Assign ranks inside each partition, based on the ordered rows.

* If rows tie (same `year`), they get the **same rank**.
* Next rank is skipped.

**Partition for product 1:**

| product\_id | year | quantity | price | rank |                  |
| ----------- | ---- | -------- | ----- | ---- | ---------------- |
| 1           | 2018 | 10       | 50    | 1    |                  |
| 1           | 2018 | 12       | 55    | 1    |                  |
| 1           | 2019 | 20       | 60    | 3    | ← rank 2 skipped |

**Partition for product 2:**

| product\_id | year | quantity | price | rank |                  |
| ----------- | ---- | -------- | ----- | ---- | ---------------- |
| 2           | 2020 | 15       | 40    | 1    |                  |
| 2           | 2020 | 18       | 42    | 1    |                  |
| 2           | 2021 | 25       | 45    | 3    | ← rank 2 skipped |

---

## 🔹 Step 4: Combine Partitions (Final CTE)

Now SQL puts the partitions back together, but keeps the `rank` info.

| product\_id | year | quantity | price | rank |
| ----------- | ---- | -------- | ----- | ---- |
| 1           | 2018 | 10       | 50    | 1    |
| 1           | 2018 | 12       | 55    | 1    |
| 1           | 2019 | 20       | 60    | 3    |
| 2           | 2020 | 15       | 40    | 1    |
| 2           | 2020 | 18       | 42    | 1    |
| 2           | 2021 | 25       | 45    | 3    |

---

✅ Finally, when you add:

```sql
where rank = 1
```

you only get the **earliest year(s)** for each product.

👉 Result:

| product\_id | first\_year | quantity | price |
| ----------- | ----------- | -------- | ----- |
| 1           | 2018        | 10       | 50    |
| 1           | 2018        | 12       | 55    |
| 2           | 2020        | 15       | 40    |
| 2           | 2020        | 18       | 42    |

---


# **📝  Difference Between group and partition**




## 🔹 `GROUP BY`

* Used in **aggregate queries** (like `SUM`, `COUNT`, `AVG`, etc.).
* It **collapses rows into one row per group**.
* You lose the individual rows — you only get the aggregated result.

👉 Example:

```sql
select product_id, min(year) as first_year
from sales
group by product_id;
```

| product\_id | first\_year |
| ----------- | ----------- |
| 1           | 2018        |
| 2           | 2020        |

Here, we don’t see all the rows anymore — just one per product.

---

## 🔹 `PARTITION BY`

* Used with **window functions** (`rank()`, `row_number()`, `sum() over`, etc.).
* It **does NOT collapse rows** — it keeps all rows.
* The window function is just applied **separately within each partition (group)**.

👉 Example:

```sql
select product_id, year,
       rank() over(partition by product_id order by year) as rnk
from sales;
```

| product\_id | year | rnk |
| ----------- | ---- | --- |
| 1           | 2018 | 1   |
| 1           | 2018 | 1   |
| 1           | 2019 | 3   |
| 2           | 2020 | 1   |
| 2           | 2020 | 1   |
| 2           | 2021 | 3   |

Notice: all rows are still there ✅, but each product’s rows get ranked separately.

---

## ⚖️ Key Difference

| Feature          | `GROUP BY` 🧩                            | `PARTITION BY` 🪟                   |
| ---------------- | ---------------------------------------- | ----------------------------------- |
| Purpose          | Aggregation (collapse rows)              | Window functions (analyze rows)     |
| Rows in output   | 1 per group (reduced)                    | All rows kept                       |
| Example use case | “Give me the first year of each product” | “Rank each product’s sales by year” |

---

### 🔑 Quick Analogy

* `GROUP BY` = "Summarize per group" (like getting a **report** 📊).
* `PARTITION BY` = "Work inside each group but keep the details" (like putting a **label** 🏷️ on each row).

