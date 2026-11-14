# PortSwigger – File Path Traversal, Validation of Start of Path

**Difficulty:** Practitioner  
**Vulnerability:** Path Traversal  
**Status:** Solved

## 1. Lab Overview

This lab contains a Path Traversal vulnerability in the way product images are retrieved.

The application transmits the full file path through a request parameter and validates that the supplied path starts with the expected directory.

The objective is to retrieve the contents of the `/etc/passwd` file.

## 2. Identifying the Vulnerability

First, I selected a product and opened its image.

I intercepted the corresponding HTTP request using Burp Suite Proxy.

The intercepted request was:

```http
GET /image?filename=/var/www/images/26.jpg HTTP/2
```

I noticed that the application supplied the full file path through the `filename` parameter.

The important detail was that the application expected the path to start with:

```text
/var/www/images/
```

This indicated that the application was validating the beginning of the supplied path.

## 3. Understanding the Bypass

Although the application checked whether the path started with `/var/www/images/`, this validation did not prevent directory traversal.

By appending directory traversal sequences after the expected directory, it was possible to navigate outside the intended folder while keeping the required prefix.

I decided to test this behavior using Burp Suite Intruder.

## 4. Automating the Attack with Burp Suite Intruder

I sent the intercepted request to Burp Suite Intruder.

I replaced the filename after the expected directory with a payload position.

The modified request looked like this:

```http
GET /image?filename=/var/www/images/placeholder/etc/passwd HTTP/2
```

I prepared the following payload list:

```text
..
../..
../../..
../../../..
```

These payloads were designed to navigate through parent directories.

After configuring the payload position and payload list, I started the Intruder attack.

## 5. Analyzing the Results

When the attack finished, I noticed that two payloads returned HTTP 200 OK responses:

```text
../../..
../../../..
```

I inspected the response contents and confirmed that the server returned the contents of `/etc/passwd`.

The lab was successfully completed!

## 6. Conclusion & Lessons Learned

Key takeaways:

- Understanding full-path validation.
- Learning how directory traversal can bypass weak path validation.
- Understanding why checking only the beginning of a file path is insufficient.
- Using `../` sequences to navigate outside an intended directory.
- Automating payload testing with Burp Suite Intruder.
- Analyzing HTTP responses to identify successful exploitation.

**Security takeaway:** Validating only the beginning of a file path is insufficient. Applications should canonicalize the complete path and verify that the resolved file remains inside the intended directory.
