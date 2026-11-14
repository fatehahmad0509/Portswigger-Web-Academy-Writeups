# PortSwigger – File Path Traversal, Validation of File Extension with Null Byte Bypass

**Difficulty:** Practitioner  
**Vulnerability:** Path Traversal  
**Bypass Technique:** Null Byte Injection (`%00`)  
**Status:** Solved

## 1. Lab Overview

This lab contains a Path Traversal vulnerability in the way product images are retrieved.

The application validates that the supplied filename ends with the expected file extension (`.jpg`).

The objective is to bypass this validation and retrieve the contents of the `/etc/passwd` file.

## 2. Identifying the Vulnerability

First, I selected a product and opened its image.

I intercepted the corresponding HTTP request using Burp Suite Proxy.

The intercepted request was:

```http
GET /image?filename=66.jpg HTTP/2
```

I noticed that the application used the `filename` parameter to retrieve images and validated whether the supplied filename ended with `.jpg`.

This meant that simply requesting `/etc/passwd` would not satisfy the application's extension validation.

## 3. Understanding the Null Byte Bypass

I decided to use a null byte (`%00`) to bypass the file extension validation.

The idea was to place `%00` after the target filename and append `.jpg` afterward.

In vulnerable systems, a null byte can act as a string terminator in certain file-handling operations.

This can cause the application to validate the `.jpg` extension while the underlying file operation processes only the part before the null byte.

## 4. Automating the Attack with Burp Suite Intruder

I sent the intercepted request to Burp Suite Intruder.

I marked the `filename` parameter as the payload position.

My modified request structure was:

```http
GET /image?filename=payloadpos/etc/passwd%00.jpg HTTP/2
```

I prepared the following payload list:

```text
..
../..
../../..
../../../..
```

The complete payloads were combined with the target file path and null byte suffix.

I then started the Intruder attack.

## 5. Analyzing the Results

After the attack finished, I noticed that the following payloads were successful:

```text
../../..
../../../..
```

Both returned HTTP 200 OK responses.

I inspected the response contents and confirmed that the server returned the contents of `/etc/passwd`.

The lab was successfully completed!

## 6. Conclusion & Lessons Learned

Key takeaways:

- Understanding file extension validation.
- Learning about null byte injection (`%00`).
- Understanding how null bytes can affect file-handling operations.
- Combining Path Traversal with a validation bypass.
- Using Burp Suite Intruder to automate payload testing.
- Analyzing HTTP responses to confirm successful exploitation.

**Security takeaway:** File extension validation alone is not sufficient to secure file access. Applications should use safe file-handling APIs, reject null bytes, canonicalize paths, and enforce directory restrictions.
