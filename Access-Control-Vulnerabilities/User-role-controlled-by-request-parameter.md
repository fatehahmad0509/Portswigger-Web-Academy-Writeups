# Lab: User role controlled by request parameter

**Difficulty:** Apprentice  
**Vulnerability:** Broken Access Control – Privilege Escalation

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Inspected the browser cookies and found two cookies:
   - `session`
   - `Admin`

3. Noticed that the `Admin` cookie was set to `false`.

4. Changed the `Admin` cookie value from `false` to `true`.

5. Navigated to `/admin` and successfully accessed the admin panel.

6. Deleted the user `carlos`.

**Result:** Lab successfully solved.

## What I Learned

The application relied on a client-side cookie to determine whether a user had administrator privileges. Since the cookie could be modified, an ordinary user could gain unauthorized administrative access.
