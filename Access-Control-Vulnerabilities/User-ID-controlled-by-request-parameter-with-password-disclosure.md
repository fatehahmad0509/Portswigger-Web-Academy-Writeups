# Lab: User ID controlled by request parameter with password disclosure

**Difficulty:** Apprentice  
**Vulnerability:** IDOR – Password Disclosure – Broken Access Control

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Navigated to the account page:

   `/my-account?id=wiener`

3. Suspected an IDOR vulnerability and changed the `id` parameter to `administrator`:

   `/my-account?id=administrator`

4. Successfully accessed the administrator's account page.

5. Noticed that the password field appeared masked in the browser interface.

6. Intercepted the `GET /my-account?id=administrator` request using Burp Suite and inspected the HTML response.

7. Found that the password was stored directly in the password input's `value` attribute:

   `<input required type=password name=password value='[REDACTED]'/>`

8. Retrieved the administrator's password from the HTML source.

9. Logged in using the administrator credentials and accessed the admin panel.

10. Deleted the user `carlos`.

**Result:** Lab successfully solved.

## What I Learned

Masking a password input with `type="password"` does not protect the underlying value.

Sensitive information should never be included in HTML responses unless absolutely necessary.

Combined with an IDOR vulnerability, this can expose another user's credentials and lead to account compromise.
