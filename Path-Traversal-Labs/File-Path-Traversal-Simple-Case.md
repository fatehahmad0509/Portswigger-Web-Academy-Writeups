# PortSwigger – File Path Traversal, Simple Case

**Difficulty:** Apprentice  
**Vulnerability:** Path Traversal  
**Status:** Solved

## 1. Lab Overview

This lab contains a Path Traversal vulnerability in the way product images are retrieved from the server.

The objective is to retrieve the contents of the `/etc/passwd` file by exploiting the vulnerable `filename` parameter.

## 2. Identifying the Vulnerability

First, I selected a product on the website and opened its image.

I intercepted the corresponding HTTP request using Burp Suite Proxy.

The intercepted request was:

```http
GET /image?filename=8.jpg HTTP/2
Host: 0a7700240324d8a085f52b9600a9003a.web-security-academy.net
```

I noticed that the image filename was supplied through the `filename` parameter.

Since this parameter was user-controllable, I decided to test it for a possible Path Traversal vulnerability.

## 3. Testing with Burp Suite Intruder

I sent the intercepted request to Burp Suite Intruder.

Then, I marked the value of the `filename` parameter as the payload position.

I created the following payload list:

```text
..
../..
../../..
../../../..
```

These payloads attempt to navigate to parent directories using the `../` sequence.

After configuring the payload list, I started the Intruder attack.

## 4. Analyzing the Results

Once the attack finished, I noticed that the following payloads returned HTTP 200 OK responses:

```text
../../..
../../../..
```

I inspected the response contents and confirmed that the server had returned the contents of the `/etc/passwd` file.

This confirmed that the application was vulnerable to Path Traversal because it allowed user-controlled input to access files outside the intended directory.

## 5. Conclusion & Lessons Learned

The lab was successfully completed!

Key takeaways:

- Understanding how Path Traversal vulnerabilities work.
- Identifying potentially vulnerable file-related parameters.
- Understanding the purpose of the `../` directory traversal sequence.
- Intercepting and modifying HTTP requests with Burp Suite Proxy.
- Automating payload testing with Burp Suite Intruder.
- Analyzing HTTP status codes and response contents to identify successful requests.

**Security takeaway:** Applications should never trust user-controlled file paths. Input validation, canonical path checks, and proper file access restrictions are essential to prevent unauthorized file access.
