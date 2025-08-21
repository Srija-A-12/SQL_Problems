# 📧 LeetCode 517: Find Users With Valid E-Mails

## 📝 Problem Statement
We are given a table `Users` containing user information along with their emails.  
Some of these emails are invalid. The task is to **return all users who have valid emails**.

---

## ✅ Rules for a Valid Email
1. **Prefix (before `@`)**
   - Must **start with a letter** (A–Z or a–z).
   - Can contain only:
     - Letters (A–Z, a–z)  
     - Digits (0–9)  
     - Underscores `_`  
     - Periods `.`  
     - Dashes `-`

2. **Domain**
   - Must be exactly: `@leetcode.com`

---

## 📊 Example

### Input
| user_id | name      | mail                    |
|---------|-----------|-------------------------|
| 1       | Winston   | winston@leetcode.com    |
| 2       | Jonathan  | jonathanisgreat         |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |
| 5       | Marwan    | quarz#2020@leetcode.com |
| 6       | David     | david69@gmail.com       |
| 7       | Shapiro   | .shapo@leetcode.com     |

### Output
| user_id | name      | mail                    |
|---------|-----------|-------------------------|
| 1       | Winston   | winston@leetcode.com    |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |

---

## 💡 Approach
We use **regular expressions** to filter out invalid emails:

- `^` → Start of string  
- `[A-Za-z]` → Must begin with a letter  
- `[A-Za-z0-9._-]*` → Allowed characters after the first letter  
- `@leetcode\.com$` → Must end with `@leetcode.com`  
- `'c'` → Case-sensitive match  

---

## 🖥️ SQL Solution

```sql
SELECT *
FROM Users
WHERE REGEXP_LIKE(mail, '^[A-Za-z][A-Za-z0-9._-]*@leetcode\\.com$', 'c');

```

---

| Pattern           | Meaning                                             |
| ----------------- | --------------------------------------------------- |
| `^`               | Start of string                                     |
| `[A-Za-z]`        | First character must be a letter                    |
| `[A-Za-z0-9._-]*` | Allowed characters (letters, digits, `_`, `.`, `-`) |
| `@leetcode\.com$` | Must end with domain `@leetcode.com`                |
| `'c'`             | Case-sensitive                                      |

---


Time Complexity: O(N) – Each email is checked once.

Space Complexity: O(1) – Constant extra space.
