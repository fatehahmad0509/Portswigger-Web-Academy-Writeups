# Lab: CSRF Where Token Is Not Tied to User Session

**Difficulty:** Practitioner  
**Vulnerability:** Cross-Site Request Forgery (CSRF)  
**Status:** Solved ✅

## 1. Lab Overview

This lab contains a CSRF vulnerability in the email change functionality. The application uses CSRF tokens, but they are not tied to the user's session.

**Objective:** Host an HTML page on the Exploit Server that performs a CSRF attack to change the victim's email address.

## 2. Obtaining a CSRF Token from the Wiener Account

I first logged in using the credentials `wiener:peter` and triggered an email change request.

I intercepted the request using Burp Suite and saved the CSRF token for later testing.

The token was:

```text
NyEQk4qxlNZd6X1dmaMLdw8ZVFelZLn6
```

I then moved on to the second account to investigate whether the token was associated with the session that generated it.

## 3. Testing the Token with the Carlos Account

I logged in using the credentials `carlos:montoya` and intercepted an email change request.

I replaced Carlos's CSRF token with the token obtained from the Wiener account and sent the request.

The server returned:

```http
HTTP/2 400 Bad Request
```

At first, I suspected that the token was invalid for Carlos's session. However, further testing revealed an important detail: the tokens were single-use. Once a token was used, it could no longer be reused.

## 4. Preserving an Unused Token

To investigate further, I generated a new CSRF token while logged in as Wiener.

Instead of allowing the request to proceed, I used Burp Suite's **Drop** functionality to prevent the request from reaching the server. This allowed me to preserve the token without consuming it.

The new token was:

```text
PPhz3Pdyz2mH1VB5XkAbIKzdg126cEdo
```

I then logged in as Carlos, intercepted a fresh email change request, replaced Carlos's token with Wiener's unused token, and sent the modified request.

This time, the request succeeded.

I checked Carlos's account page and confirmed that the email address had changed.

**Key finding:** A valid CSRF token generated in one user's session was accepted in another user's session, provided the token had not already been used. This demonstrated that the token was not tied to the session that generated it.

## 5. Obtaining a Token for the Final Exploit

After confirming the vulnerability, I logged in as Wiener again and generated another CSRF token.

Using the same technique, I dropped the request before it reached the server so that the token remained unused.

The token reserved for the final exploit was:

```text
i4rs0wVdG0GSiGOL1RhZGbD03m7FVj5a
```

I used this token in the HTML page hosted on the Exploit Server.

## 6. Creating the CSRF Exploit

I prepared the following HTML page:

```html
<form action="https://0a790018043291b887f856f300b700a4.web-security-academy.net/my-account/change-email" method="POST">
    <input type="hidden" name="email" value="fateh2011521@smail.com">
    <input type="hidden" name="csrf" value="i4rs0wVdG0GSiGOL1RhZGbD03m7FVj5a">
</form>

<script>
    document.forms[0].submit();
</script>
```

The form contains two hidden parameters:

- `email`: The email address to which the victim's account will be changed.
- `csrf`: A valid, unused token generated in the Wiener account.

The form automatically submits a `POST` request when the page loads.

Because the application does not bind the token to the session that generated it, the token can be used in a request authenticated with a different user's session.

## 7. Delivering the Exploit

I hosted the HTML page on the Exploit Server and delivered it through the lab's exploit workflow.

The exploit succeeded, and the lab was marked as solved.

## 8. Root Cause and Key Takeaways

The root cause is that CSRF tokens are not associated with the session of the user for whom they were generated.

The application checks whether the token is valid, but it does not verify that the token belongs to the current user's session.

Key takeaways:

- CSRF tokens must be bound to the appropriate user session.
- A token that is valid but not session-bound may be reusable across different users' sessions.
- Single-use tokens introduce an additional consideration during testing because using a token can invalidate it.
- Burp Suite's Drop functionality can help preserve a request for controlled testing when the request has not yet reached the server.
- Cross-account testing can reveal flaws that would not be apparent when testing only one account.

### Recommended Mitigation

Generate unpredictable CSRF tokens and associate them with the session for which they were issued. On every state-changing request, verify that the submitted token is valid and belongs to the current session. Reject missing, invalid, expired, or mismatched tokens.
