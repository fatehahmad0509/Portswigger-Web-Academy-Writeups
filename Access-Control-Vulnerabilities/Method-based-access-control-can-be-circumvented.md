# Lab: Method-based access control can be circumvented

**Difficulty:** Practitioner  
**Vulnerability:** Method-based Access Control Bypass

## Steps

1. Logged in using the administrator credentials:
   - Username: `administrator`
   - Password: `admin`

2. Accessed the admin panel and inspected the user role management functionality.

3. Intercepted the role upgrade request using Burp Suite.

4. Identified the following POST request:

   ```http
   POST /admin-roles
   ```

   Request body:

   ```text
   username=wiener&action=upgrade
   ```

5. Logged out of the administrator account and logged in using the low-privileged account:

   - Username: `wiener`
   - Password: `peter`

6. Replayed the POST request using Wiener's session cookie. The server returned `401 Unauthorized`.

7. Changed the HTTP method from POST to GET and moved the parameters into the URL:

   ```http
   GET /admin-roles?username=wiener&action=upgrade
   ```

8. Sent the modified request using Wiener's session cookie.

9. The server accepted the request and successfully upgraded Wiener's role to administrator.

**Result:** Lab successfully solved.

## What I Learned

Method-based access control can be vulnerable when an application enforces authorization checks differently depending on the HTTP method.

In this lab, the POST request was properly restricted, but the equivalent GET request did not enforce the same authorization checks.

Changing the HTTP method allowed a low-privileged user to perform an administrative action.
