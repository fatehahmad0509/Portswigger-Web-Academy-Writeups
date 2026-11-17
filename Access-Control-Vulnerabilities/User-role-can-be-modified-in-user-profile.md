# Lab: User role can be modified in user profile

**Difficulty:** Apprentice  
**Vulnerability:** Mass Assignment – Broken Access Control – Privilege Escalation

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Inspected the application interface but couldn't find any direct role modification functionality.

3. Noticed the email change feature and intercepted its request using Burp Suite.

4. Examined the response and discovered that the API returned user information, including:
   - `username`
   - `email`
   - `apikey`
   - `roleid`

5. Observed that the current `roleid` was `1`.

6. Suspected a Mass Assignment vulnerability and added `"roleid": 2` to the JSON request body.

7. The server accepted the additional parameter and updated the account's `roleid` to `2`.

8. Accessed `/admin` successfully and deleted the user `carlos`.

**Result:** Lab successfully solved.

## What I Learned

Mass Assignment vulnerabilities can occur when an application automatically assigns user-supplied request parameters to internal object properties without properly restricting which fields can be modified.

In this lab, the email change endpoint also allowed modification of the `roleid` property, resulting in unauthorized privilege escalation.
