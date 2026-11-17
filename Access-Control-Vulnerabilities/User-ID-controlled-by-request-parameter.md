# Lab: User ID controlled by request parameter

**Difficulty:** Apprentice  
**Vulnerability:** IDOR (Insecure Direct Object Reference) – Horizontal Privilege Escalation

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Navigated to the account page:

   `/my-account?id=wiener`

3. Suspected a possible IDOR vulnerability because the user ID was directly controlled by a URL parameter.

4. Changed the `id` parameter from `wiener` to `carlos`:

   `/my-account?id=carlos`

5. Successfully accessed Carlos's account information without authorization.

6. Retrieved Carlos's API key and submitted it as the lab solution.

**Result:** Lab successfully solved.

## What I Learned

IDOR vulnerabilities occur when an application uses user-controlled identifiers to access resources without properly checking whether the current user is authorized to access them.

In this lab, changing the `id` parameter allowed me to access another user's account information, including their API key.
