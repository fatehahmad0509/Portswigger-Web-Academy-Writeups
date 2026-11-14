# Lab: File Path Traversal, Traversal Sequences Stripped Non-Recursively

**Difficulty:** Practitioner  
**Vulnerability:** Path Traversal

### Steps

First, I intercepted the image request using Burp Suite:

```http
GET /image?filename=41.jpg HTTP/2
```

I initially changed the `filename` parameter to `../41.jpg` to test whether the application allowed directory traversal.

The application stripped the `../` sequence, so I remained in the original directory and the image was displayed normally.

I then considered how to bypass this filtering mechanism. Since the application removes traversal sequences non-recursively, I could use a nested sequence:

```text
....//
```

After the application removes the inner `../`, another `../` remains.

I tested this by changing the filename parameter to:

```text
....//41.jpg
```

The response returned `No such file`, confirming that the traversal sequence bypass worked and that I had moved to the parent directory.

### Exploitation

Next, I sent the request to Burp Suite Intruder and replaced the filename parameter with a payload position.

I prepared the following payload list:

```text
....//
....//....//
....//....//....//
....//....//....//....//
```

I then appended `etc/passwd` to the payload position:

```http
GET /image?filename=§payload§etc/passwd HTTP/2
```

After starting the Intruder attack, I examined the responses.

Two payloads returned HTTP 200 responses:

```text
....//....//....//
....//....//....//....//
```

These responses successfully returned the contents of `/etc/passwd`.

**Result:** Lab successfully completed.

### What I Learned

- How non-recursive path traversal filtering works.
- How nested traversal sequences can bypass simple filtering mechanisms.
- How to use Burp Suite Intruder to test multiple payloads efficiently.
- How HTTP response codes and response contents can help identify successful path traversal attempts.
