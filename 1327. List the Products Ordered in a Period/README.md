
## 📘 Problem Recap

We have two tables:

### **Products**

| product\_id | product\_name |
| ----------- | ------------- |
| 1           | Apple         |
| 2           | Orange        |
| 3           | Banana        |

### **Orders**

| product\_id | unit | order\_date |
| ----------- | ---- | ----------- |
| 1           | 50   | 2020-02-05  |
| 1           | 70   | 2020-02-20  |
| 2           | 30   | 2020-01-25  |
| 3           | 120  | 2020-02-10  |

👉 We want: **product names with total units ≥ 100 in February 2020 (2020-02-01 to 2020-02-29).**

---

## 📝 Query Explained

```sql
select a.product_name, sum(b.unit) as unit 
from Products a
right join Orders b
  on a.product_id = b.product_id
where b.order_date >= '2020-02-01'
  and b.order_date < '2020-03-01'
group by a.product_id, a.product_name
having sum(b.unit) >= 100;
```

---

### 🔎 Step-by-Step

1️⃣ **`right join Orders b on a.product_id = b.product_id`**

* Ensures all matching orders are included, even if a product is missing from Products.
* Joins product names to order details.

👉 After join:

| product\_id | product\_name | unit | order\_date |
| ----------- | ------------- | ---- | ----------- |
| 1           | Apple         | 50   | 2020-02-05  |
| 1           | Apple         | 70   | 2020-02-20  |
| 2           | Orange        | 30   | 2020-01-25  |
| 3           | Banana        | 120  | 2020-02-10  |

---

2️⃣ **`where b.order_date >= '2020-02-01' and b.order_date < '2020-03-01'`**

* Filters only **February 2020** orders.

👉 Now table looks like:

| product\_id | product\_name | unit | order\_date |
| ----------- | ------------- | ---- | ----------- |
| 1           | Apple         | 50   | 2020-02-05  |
| 1           | Apple         | 70   | 2020-02-20  |
| 3           | Banana        | 120  | 2020-02-10  |

---

3️⃣ **`group by a.product_id, a.product_name`**

* Groups rows per product so we can sum their units.

👉 Group results:

* Apple → 50 + 70 = 120
* Banana → 120

---

4️⃣ **`having sum(b.unit) >= 100`**

* Keeps only those products with **≥ 100 units sold**.

👉 Final Result:

| product\_name | unit |
| ------------- | ---- |
| Apple         | 120  |
| Banana        | 120  |

---

## ✅ Output

The query returns all **products with sales ≥ 100 units in Feb 2020**.

---

⚡ Extra tip:
Since `Orders` always has product\_id, you can also safely use **INNER JOIN** instead of RIGHT JOIN here (faster in many cases).

