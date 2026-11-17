# Lab: Referer-based access control

**Difficulty:** Practitioner  
**Vulnerability:** Broken Access Control  
**Category:** Referer-based Access Control Bypass

## Summary

The application uses the HTTP `Referer` header to control access to administrative functionality.

However, the server trusts this client-controlled header instead of properly verifying the user's permissions. By sending an administrative Referer value with a regular user's session, an attacker can bypass the access control mechanism.

## Steps to Reproduce

1. Log in using the administrator credentials:
   - Username: `administrator`
   - Password: `admin`

2. Navigate to the admin panel and capture the role-upgrade request.

3. Identify the request:

```http
GET /admin-roles?username=carlos&action=upgrade HTTP/2
Referer: https://LAB-ID.web-security-academy.net/admin
```

4. Log out and authenticate as a regular user:
   - Username: `wiener`
   - Password: `peter`

5. Capture the role-upgrade request and replace its session cookie with Wiener's session cookie.

6. Keep the Referer header pointing to the admin page:

```http
Referer: https://LAB-ID.web-security-academy.net/admin
```

7. Change the username parameter to `wiener`:

```http
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Cookie: session=WIENER_SESSION
Referer: https://LAB-ID.web-security-academy.net/admin
```

8. Send the modified request.

9. The server processes the request and promotes Wiener to administrator.

## Result

The lab is successfully solved. A regular user can bypass the administrative access control by supplying a forged Referer header.

## What I Learned

- The Referer header identifies the page from which a request originated.
- HTTP headers supplied by clients cannot be trusted for authorization.
- Access control must be enforced using server-side authentication and authorization checks.
- Administrative actions must verify the authenticated user's actual privileges.
