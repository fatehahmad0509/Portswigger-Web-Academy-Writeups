# Lab: User ID controlled by request parameter with data leakage in redirect

**Difficulty:** Apprentice  
**Vulnerability:** IDOR – Information Disclosure in Redirect Response

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Intercepted the account page request using Burp Suite:

   `GET /my-account?id=wiener`

3. Changed the `id` parameter from `wiener` to `carlos`.

4. Forwarded the modified request:

   `GET /my-account?id=carlos`

5. The server returned Carlos's account information, even though the response involved a redirect.

6. Retrieved Carlos's API key from the response and submitted it as the lab solution.

**Result:** Lab successfully solved.

## What I Learned

HTTP redirect responses can still contain sensitive information in their response bodies.

An application must properly enforce authorization before returning sensitive user data. A redirect does not replace access control checks.
