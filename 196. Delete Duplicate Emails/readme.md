
# 📝 README — Delete Duplicate Emails in `Person`

We want to **remove duplicate rows** from the `Person` table based on the `Email`.
Keep the row with the **smallest `Id`**, delete others.

---

## 🔹 Query

```sql
DELETE p 
FROM Person p, Person q
WHERE p.Id > q.Id 
  AND q.Email = p.Email;
```

---

## 1. Input Example — `Person` Table

| Id | Email                                       |
| -- | ------------------------------------------- |
| 1  | [alice@mail.com](mailto:alice@mail.com)     |
| 2  | [bob@mail.com](mailto:bob@mail.com)         |
| 3  | [alice@mail.com](mailto:alice@mail.com)     |
| 4  | [alice@mail.com](mailto:alice@mail.com)     |
| 5  | [charlie@mail.com](mailto:charlie@mail.com) |

---

## 2. Breaking the Query

### 🔸 `FROM Person p, Person q`

* This makes a **self-join**: compare each row (`p`) with each other row (`q`).

---

### 🔸 `WHERE q.Email = p.Email`

* Only look at pairs where the **email is the same**.
  Example pairs found:
* `(p=3, q=1)` → same email = `alice@mail.com`
* `(p=4, q=1)` → same email = `alice@mail.com`
* `(p=4, q=3)` → same email = `alice@mail.com`

---

### 🔸 `AND p.Id > q.Id`

* Keep only the pairs where `p.Id` is **greater** (a duplicate that comes later).
  So:
* `(p=3, q=1)` ✅ delete Id=3
* `(p=4, q=1)` ✅ delete Id=4
* `(p=4, q=3)` ✅ delete Id=4

---

### 🔸 `DELETE p`

* Delete those duplicate rows (`p`).

---

## 3. Output — Cleaned Table

After deletion, we keep only **the earliest (smallest Id)** row per email:

| Id | Email                                       |
| -- | ------------------------------------------- |
| 1  | [alice@mail.com](mailto:alice@mail.com)     |
| 2  | [bob@mail.com](mailto:bob@mail.com)         |
| 5  | [charlie@mail.com](mailto:charlie@mail.com) |

---

## ✅ In Simple Words

1. Compare each row with others in the same table.
2. If two rows have the same email, remove the one with the bigger `Id`.
3. This ensures only the **first occurrence** of each email remains.


