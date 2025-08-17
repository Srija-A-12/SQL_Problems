

# 📝 README — Find Patients With Diabetes Code `DIAB1`

We are using:

```sql
# Write your MySQL query statement below
select patient_id, patient_name, conditions
from Patients
where conditions like "% DIAB1%"
   or conditions like "DIAB1%";
```

---

## 1. Input Table — `Patients`

| patient\_id | patient\_name | conditions         |
| ----------- | ------------- | ------------------ |
| 1           | Alice         | DIAB100            |
| 2           | Bob           | HYPERTENSION DIAB1 |
| 3           | Charlie       | DIAB1 ASTHMA       |
| 4           | David         | HEART DIAB12       |
| 5           | Eva           | CANCER             |

---

## 2. Understanding `LIKE` and `%`

* `LIKE` is used for **pattern matching**.
* `%` is a **wildcard** → it means “match any sequence of characters (including none).”

### Types of usage:

1. **`'DIAB1%'`**

   * Pattern must **start with `DIAB1`**.
   * Examples that match:

     * `DIAB1`
     * `DIAB100`
     * `DIAB1 ASTHMA`
   * Doesn’t match:

     * `HYPERTENSION DIAB1` (because it starts with `HYPERTENSION`).

2. **`'% DIAB1%'`**

   * Pattern must have a **space before `DIAB1`** (to avoid matching things like `DIAB12`).
   * Matches when `DIAB1` appears **in the middle** of the string.
   * Examples that match:

     * `HYPERTENSION DIAB1`
     * `ASTHMA DIAB1 FEVER`
   * Doesn’t match:

     * `DIAB100` (no space before).

---

## 3. Applying the Conditions

### Condition 1 → `conditions like 'DIAB1%'`

| patient\_id | conditions         | Matches? |
| ----------- | ------------------ | -------- |
| 1           | DIAB100            | ✅ Yes    |
| 2           | HYPERTENSION DIAB1 | ❌ No     |
| 3           | DIAB1 ASTHMA       | ✅ Yes    |
| 4           | HEART DIAB12       | ❌ No     |
| 5           | CANCER             | ❌ No     |

---

### Condition 2 → `conditions like '% DIAB1%'`

| patient\_id | conditions         | Matches?                       |
| ----------- | ------------------ | ------------------------------ |
| 1           | DIAB100            | ❌ No                           |
| 2           | HYPERTENSION DIAB1 | ✅ Yes                          |
| 3           | DIAB1 ASTHMA       | ❌ No (no space before `DIAB1`) |
| 4           | HEART DIAB12       | ❌ No                           |
| 5           | CANCER             | ❌ No                           |

---

## 4. Final Output (Union of Both Conditions)

| patient\_id | patient\_name | conditions         |
| ----------- | ------------- | ------------------ |
| 1           | Alice         | DIAB100            |
| 2           | Bob           | HYPERTENSION DIAB1 |
| 3           | Charlie       | DIAB1 ASTHMA       |

---

## ✅ Explanation in Simple Words

* `DIAB1%` → finds patients whose condition **starts with `DIAB1`**.
* `% DIAB1%` → finds patients who have `DIAB1` **somewhere in the middle**, with a space before it.
* Together, these cover patients where `DIAB1` is at the **start** or in the **middle** of the condition list.

---

# Let’s add a **visual cheat-sheet** for all `LIKE` + `%` cases so you can use it in your README.

---

# 📝 README — MySQL `LIKE` Wildcard Patterns (`%`)

The symbol `%` means **match any sequence of characters (0 or more)**.
Below are all the common usage patterns:

---

## 1. **Starts With**

```sql
column LIKE 'abc%'
```

* ✅ Matches: `abc`, `abcd`, `abc123`
* ❌ Doesn’t Match: `xabc`, `aabc`

---

## 2. **Ends With**

```sql
column LIKE '%abc'
```

* ✅ Matches: `abc`, `xabc`, `123abc`
* ❌ Doesn’t Match: `abcd`, `abcx`

---

## 3. **Contains (Anywhere)**

```sql
column LIKE '%abc%'
```

* ✅ Matches: `abc`, `xabc`, `123abc456`, `myabcfile`
* ❌ Doesn’t Match: `acb`, `abbc`

---

## 4. **Exact Word with Space Before**

```sql
column LIKE '% abc%'
```

* ✅ Matches: `hello abc`, `xyz abc123`
* ❌ Doesn’t Match: `abcde`, `myabc`

---

## 5. **Single Character Wildcard (`_`)**

* `_` = match **exactly one character** (not 0).

```sql
column LIKE 'a_c'
```

* ✅ Matches: `abc`, `a1c`, `axc`
* ❌ Doesn’t Match: `ac`, `abbc`

---

## 6. **Mixing `%` and `_`**

```sql
column LIKE 'a_%_c'
```

* ✅ Matches: `aXc`, `a123c`, `a_hello_c`
* ❌ Doesn’t Match: `ac`, `aaac`

---

## 🔍 Quick Visual Table

| Pattern    | Meaning                        | Example Match | Example No-Match |
| ---------- | ------------------------------ | ------------- | ---------------- |
| `'abc%'`   | Starts with "abc"              | `abc123`      | `xabc`           |
| `'%abc'`   | Ends with "abc"                | `123abc`      | `abcx`           |
| `'%abc%'`  | Contains "abc" anywhere        | `123abc456`   | `abbc`           |
| `'% abc%'` | Has "abc" with space before it | `test abc123` | `abcde`          |
| `'a_c'`    | "a", then 1 char, then "c"     | `axc`         | `ac`             |
| `'a_%_c'`  | "a", then many chars, then "c" | `a123c`       | `ac`             |

---


