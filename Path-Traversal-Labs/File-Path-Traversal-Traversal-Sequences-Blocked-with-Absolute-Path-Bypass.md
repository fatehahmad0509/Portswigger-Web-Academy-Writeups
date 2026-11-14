# PortSwigger – File Path Traversal, Traversal Sequences Blocked with Absolute Path Bypass

**Difficulty:** Practitioner  
**Vulnerability:** Path Traversal  
**Status:** Solved

## 1. Lab Overview

This lab contains a Path Traversal vulnerability in the way product images are retrieved.

The application blocks directory traversal sequences but treats the supplied filename as relative to a default working directory.

The objective is to retrieve the contents of the `/etc/passwd` file.

## 2. Identifying the Vulnerability

First, I selected a product and opened its image.

I intercepted the image request using Burp Suite Proxy.

The request contained the following parameter:

```http
GET /image?filename=8.jpg HTTP/2
```

I noticed that the `filename` parameter controlled which image was retrieved by the server.

## 3. Exploiting the Vulnerability

Since the application blocked directory traversal sequences such as `../`, I decided to try using an absolute file path instead.

I modified the `filename` parameter directly:

```http
GET /image?filename=/etc/passwd HTTP/2
```

Instead of using directory traversal sequences, I supplied the absolute path of the target file.

## 4. Result

After forwarding the modified request, the server returned the contents of `/etc/passwd`.

The lab was immediately marked as solved!

## 5. Conclusion & Lessons Learned

The lab was successfully completed.

Key takeaways:

- Understanding how applications handle file paths.
- Recognizing that blocking `../` sequences alone is not sufficient protection.
- Learning how absolute file paths can bypass weak traversal protections.
- Modifying HTTP parameters using Burp Suite Proxy.

**Security takeaway:** Blocking known traversal sequences is not enough to prevent Path Traversal. Applications should validate and canonicalize file paths and restrict file access to explicitly permitted directories.
