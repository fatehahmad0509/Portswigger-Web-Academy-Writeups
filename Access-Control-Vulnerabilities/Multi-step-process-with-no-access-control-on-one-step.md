# Lab: Multi-step process with no access control on one step

**Difficulty:** Practitioner  
**Vulnerability:** Broken Access Control  
**Category:** Multi-step Process Access Control Bypass

## Summary

The application uses a multi-step process to change user roles. However, access control is not properly enforced at every step.

Although the initial request requires administrator privileges, the confirmation request can be submitted by a regular user without proper authorization.

## Steps to Reproduce

1. Log in using the administrator credentials:
   - Username: `administrator`
   - Password: `admin`

2. Navigate to the admin panel and capture the role-upgrade requests.

3. Identify the two requests sent during the role-change process.

**First request:**
```http
POST /admin-roles

username=carlos&action=upgrade
```

**Second request:**
```http
POST /admin-roles

action=upgrade&confirmed=true&username=Carlos
```

4. Log out and authenticate as a regular user:
   - Username: `wiener`
   - Password: `peter`

5. Copy the Wiener session cookie.

6. Replace the session cookie in the second request with Wiener's session cookie.

7. Change the username parameter to `wiener`:

```http
POST /admin-roles

action=upgrade&confirmed=true&username=wiener
```

8. Send the modified request.

9. The application processes the request and promotes Wiener to administrator.

## Result

The lab is successfully solved. A regular user can promote their own account to administrator by directly submitting the second step of the role-change process.

## What I Learned

- Access control must be enforced at every step of a multi-step process.
- Protecting the initial request does not automatically protect subsequent requests.
- Sensitive actions should always be authorized server-side.
- A user must not be able to complete an administrative action simply by submitting its confirmation request directly.
