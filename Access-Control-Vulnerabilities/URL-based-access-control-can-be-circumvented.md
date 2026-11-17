# Lab: URL-based access control can be circumvented

**Difficulty:** Practitioner  
**Vulnerability:** URL-based Access Control Bypass – HTTP Header Manipulation

## Steps

1. Navigated to `/admin` and received an `Access denied` response.

2. Intercepted the request using Burp Suite.

3. Changed the request path from `/admin` to `/` and added the following header:

   `X-Original-URL: /admin`

4. Forwarded the modified request and successfully accessed the admin panel.

5. Inspected the HTML response and found the delete link for Carlos:

   `/admin/delete?username=carlos`

6. Created a new request and changed the path to `/`.

7. Added the following header:

   `X-Original-URL: /admin/delete`

8. Included the username parameter:

   `?username=carlos`

9. Sent the request and successfully deleted Carlos.

**Result:** Lab successfully solved.

## What I Learned

Some applications use front-end systems to enforce URL-based access restrictions while the back-end supports alternative URL routing headers.

If these components interpret the request differently, access control restrictions can be bypassed.

The `X-Original-URL` header can influence how the back-end interprets the requested path, potentially exposing restricted functionality.
