
# 📝 README — Format User Names (First Letter Uppercase, Rest Lowercase)

We are solving this with:

```sql
# Write your MySQL query statement below
select user_id,
       concat(upper(substr(name,1,1)), lower(substr(name,2,length(name)))) as name
from Users
order by 1;
```

---

## 1. Input Table — `Users`

| user\_id | name    |
| -------- | ------- |
| 1        | aLIce   |
| 2        | BoB     |
| 3        | charLIE |

---

## 2. Breaking the Query

### 🔹 `substr(name,1,1)`

Takes the **first character** of the name.

* For `aLIce` → `a`
* For `BoB` → `B`
* For `charLIE` → `c`

---

### 🔹 `upper(substr(name,1,1))`

Converts that first character to **uppercase**.

* `a` → `A`
* `B` → `B`
* `c` → `C`

---

### 🔹 `substr(name,2,length(name))`

Takes the substring starting from the **2nd character to the end**.

* `aLIce` → `LIce`
* `BoB` → `oB`
* `charLIE` → `harLIE`

---

### 🔹 `lower(substr(name,2,length(name)))`

Makes the rest of the characters **lowercase**.

* `LIce` → `lice`
* `oB` → `ob`
* `harLIE` → `harlie`

---

### 🔹 `concat(upper(...), lower(...))`

Joins the first uppercase letter with the rest in lowercase.

* `A` + `lice` → `Alice`
* `B` + `ob` → `Bob`
* `C` + `harlie` → `Charlie`

---

## 3. Final Query Output

| user\_id | name    |
| -------- | ------- |
| 1        | Alice   |
| 2        | Bob     |
| 3        | Charlie |

---

## ✅ Explanation in Simple Words

1. Take the first character of the name and make it uppercase.
2. Take the rest of the characters and make them lowercase.
3. Combine both to form the correctly formatted name.
4. Order results by `user_id`.
