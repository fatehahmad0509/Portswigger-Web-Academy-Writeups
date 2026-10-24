# Lab: SameSite Lax Bypass via Method Override

**Difficulty:** Practitioner  
**Status:** Solved  
**Category:** Cross-Site Request Forgery (CSRF)

## 1. Objective

The objective was to exploit a CSRF vulnerability in the email-change functionality and change the victim's email address by hosting an attack on the provided exploit server.

The application used SameSite cookie restrictions, but its method-override functionality allowed the protection to be bypassed.

## 2. Reconnaissance

I logged in to my account using the provided credentials and intercepted the email-change request in Burp Suite.

The original request was:

```http
POST /my-account/change-email HTTP/2
Host: LAB-ID.web-security-academy.net
Cookie: session=SESSION_COOKIE
Content-Type: application/x-www-form-urlencoded

email=se%40gm.com
```

The endpoint accepted a `POST` request and returned a `302` redirect after processing the email change.

I also considered checking the supported HTTP methods using the `OPTIONS` method. However, the endpoint indicated that only `POST` was accepted.

## 3. Testing Method Override

I suspected that the application might support HTTP method overriding, which allows a request to be processed using a different HTTP method from the one specified in the original request.

I added the following header to the intercepted request:

```http
X-Method-Override: GET
```

The server still returned a `302` response. After checking my account, I confirmed that the email address had actually changed.

This demonstrated that the application accepted the method-override header and that the response status alone was insufficient to determine whether the attack had succeeded.

## 4. Exploitation

Next, I prepared a payload on the provided exploit server to trigger the email-change functionality in the victim's browser.

The exploit used a navigation to the vulnerable endpoint:

```html
<script>
    window.location = "https://LAB-ID.web-security-academy.net/my-account/change-email?email=NEW_EMAIL&_method=POST";
</script>
```

I replaced the lab hostname and email address with the appropriate values.

The `_method=POST` parameter instructed the application to process the request as a `POST` operation, even though the browser initiated a `GET` navigation.

Because Chrome's default SameSite=Lax behavior can allow cookies on certain top-level, cross-site navigations using safe methods such as `GET`, this technique could allow the victim's authenticated session cookie to accompany the request.

The application then processed the email-change operation using the victim's session.

## 5. Result

I delivered the exploit through the provided exploit server and confirmed that the lab was solved.

The attack succeeded because the application allowed a cross-site navigation to trigger a state-changing action through HTTP method overriding.

## 6. Key Takeaways

- **SameSite=Lax:** Understand how browser cookie restrictions affect cross-site requests.
- **HTTP method overriding:** Applications may interpret a `GET` request as another HTTP method when specific parameters or headers are present.
- **CSRF prevention:** State-changing operations should not be accessible through cross-site `GET` navigations.
- **Defense in depth:** Use proper CSRF tokens, validate request origins where appropriate, configure cookies securely, and reject unsafe method overrides on sensitive endpoints.

**Conclusion:** This lab demonstrated how HTTP method overriding can undermine assumptions about SameSite=Lax cookie protections and enable a CSRF attack against an authenticated user.
