Lab: Stored XSS into Anchor "href" Attribute with Double Quotes HTML-Encoded

Difficulty: Apprentice
Vulnerability: Stored Cross-Site Scripting (XSS)
Status: Solved

1. Lab Description

This lab contains a Stored Cross-Site Scripting (XSS) vulnerability in the comment functionality. The objective is to submit a comment that triggers the JavaScript "alert()" function when another user clicks the comment author's name.

2. Steps to Solve

1. Open the lab and navigate to the comment section of a blog post.

2. Fill in the comment form with arbitrary values for the name, email, and comment fields.

3. In the Website field, enter the following payload:
   
   javascript:alert(1)

4. Submit the comment.

5. After the comment is published, click on the comment author's name.

6. The browser executes the JavaScript payload and displays an alert, successfully solving the lab.

3. Technical Explanation

The vulnerability occurs because the application uses the user-supplied website URL to create a link associated with the comment author's name.

Although the application HTML-encodes double quotes, this protection does not prevent every type of XSS attack. The "javascript:" URL scheme can execute JavaScript when a user activates the link, provided the application does not properly validate the URL scheme.

In this case, clicking the author's name causes the browser to execute "alert(1)".

Why Does the Payload Work?

- "javascript:" specifies a JavaScript URL rather than a conventional HTTP or HTTPS URL.
- "alert(1)" invokes the browser's JavaScript alert function.
- User interaction: Clicking the author's name activates the link and triggers the payload.

4. Key Takeaways

- Stored XSS occurs when user-controlled input is saved by an application and later executed in another user's browser.
- HTML encoding alone is not sufficient to prevent every type of XSS vulnerability.
- URL validation must account for dangerous schemes such as "javascript:".
- Applications should validate user-supplied URLs using an appropriate allowlist, typically permitting only "https:" and, where necessary, "http:".
- URL validation and context-appropriate output encoding serve different purposes and should be used together where appropriate.

5. Conclusion

The lab was solved by submitting "javascript:alert(1)" in the Website field and clicking the comment author's name. This demonstrated how insufficient URL scheme validation can lead to Stored XSS, even when double quotes are HTML-encoded.