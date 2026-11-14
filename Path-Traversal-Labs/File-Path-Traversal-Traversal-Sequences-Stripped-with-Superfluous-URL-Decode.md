# PortSwigger – File Path Traversal, Traversal Sequences Stripped with Superfluous URL-Decode

**Difficulty:** Practitioner  
**Vulnerability:** Path Traversal  
**Status:** Solved

## 1. Lab Overview

This lab contains a Path Traversal vulnerability in the way product images are retrieved.

The application blocks input containing directory traversal sequences. However, it performs URL decoding after filtering the input.

The objective is to retrieve the contents of the `/etc/passwd` file.

## 2. Identifying the Vulnerability

First, I selected a product and opened its image.

I intercepted the corresponding HTTP request using Burp Suite Proxy.

The intercepted request was:

```http
GET /image?filename=10.jpg HTTP/2
```

I identified the `filename` parameter as the potential entry point for the Path Traversal vulnerability.

## 3. Testing Double URL Encoding

Since the application blocked regular traversal sequences, I decided to try double URL encoding.

I used the following payload:

```text
%252e%252e%252f
```

This represents a double-encoded version of:

```text
../
```

I sent the request and received a "No such file" response.

This suggested that the input had passed through the filtering and decoding process, but the requested file could not be found at the resulting path.

## 4. Automating the Attack with Burp Suite Intruder

Next, I sent the request to Burp Suite Intruder.

I marked the `filename` parameter as the payload position and prepared the following payload list:

```text
%252e%252e%252f
%252e%252e%252f%252e%252e%252f
%252e%252e%252f%252e%252e%252f%252e%252e%252f
%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f
```

These payloads represent different numbers of parent-directory traversal sequences.

I then started the Intruder attack.

## 5. Analyzing the Results

After the attack finished, I noticed that two payloads returned HTTP 200 OK responses:

```text
%252e%252e%252f%252e%252e%252f%252e%252e%252f

%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f
```

I inspected the responses and confirmed that both returned the contents of `/etc/passwd`.

The lab was successfully completed!

## 6. Conclusion & Lessons Learned

Key takeaways:

- Understanding double URL encoding.
- Learning how URL decoding works.
- Understanding why filtering before decoding can introduce security vulnerabilities.
- Recognizing how double-encoded traversal sequences can bypass weak input filters.
- Using Burp Suite Intruder to automate payload testing.
- Comparing HTTP responses to identify successful payloads.

**Security takeaway:** Input validation should be performed after proper decoding and normalization. Applications should avoid relying on simple string filtering to prevent Path Traversal attacks.
