
# 📘 Problem Explanation

We are given two tables:

* **Customer** (customer\_id, product\_key) → which products each customer has purchased.
* **Product** (product\_key) → list of all available products.

We want to **find customers who bought all the products**.
This is a **set division problem** → customer’s purchased set must equal the set of all products.

---

# 📝 SQL Query

```sql
select customer_id 
from Customer 
group by customer_id 
having 
    (select count(distinct product_key)
     from Product) = count(distinct product_key);
```

---

# 🔎 Step-by-Step Explanation

### 1️⃣ `group by customer_id`

We group all rows by customer\_id → so we can check what each customer has purchased.

Example:

| customer\_id | product\_key |
| ------------ | ------------ |
| 1            | A            |
| 1            | B            |
| 2            | A            |
| 2            | C            |

After grouping →

* Customer 1 → {A, B}
* Customer 2 → {A, C}

---

### 2️⃣ `(select count(distinct product_key) from Product)`

This subquery counts the **total number of unique products available**.

Example:
Product table → {A, B, C}
👉 Output = 3

---

### 3️⃣ `count(distinct product_key)` inside `having`

This counts the **unique number of products each customer has purchased**.

Example:

* Customer 1 → bought {A, B} → count = 2
* Customer 2 → bought {A, C} → count = 2

---

### 4️⃣ `having (total_products = purchased_products)`

We keep only those customers who purchased **all products**.

Example:

* Total products = 3
* Customer 1 = 2 ❌ (not enough)
* Customer 2 = 2 ❌
* If Customer 3 had {A, B, C} → count = 3 ✅ → included

---

# ✅ Output

The query returns **all customers who bought every single product** in the `Product` table.

---

# 🧠 Key Concepts

* **Group By** → aggregate purchases per customer.
* **Set Division** → "customer’s set = universal set".
* **Having Clause** → filter groups after aggregation.

