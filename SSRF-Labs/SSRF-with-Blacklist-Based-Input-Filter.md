# Lab: SSRF with Blacklist-Based Input Filter

**Difficulty:** Practitioner  
**Status:** Solved

## Objective

The application contains a stock check feature that fetches data from an internal system.

The objective is to bypass two weak anti-SSRF defenses, access the internal admin interface at `http://localhost/admin`, and delete the user `carlos`.

## Exploitation

### 1. Intercepting the stock check request

First, I intercepted the stock check request using Burp Suite Proxy.

I identified the `stockApi` parameter, which controls the URL used by the server to retrieve stock information.

### 2. Testing the localhost filter

Before attempting to bypass the filter, I tested whether the application blocked requests to `localhost`.

I used the following URL:

```http
http://localhost/admin
```

The server returned:

```http
HTTP 400 Bad Request
```

This confirmed that `localhost` was blocked by the input filter.

### 3. Bypassing the hostname restriction

Next, I tried an alternative representation of the localhost IP address:

```http
http://127.1
```

Instead of returning HTTP 400, the server returned HTTP 500.

This indicated that the hostname restriction had been bypassed, although the admin path was still inaccessible.

### 4. Bypassing the admin path restriction

I then tested URL encoding to bypass the second filter.

I used a double-encoded version of the letter `a` in the `/admin` path:

```http
http://127.1/%2561dmin
```

The server returned:

```http
HTTP 200 OK
```

The admin interface was now accessible!

### 5. Deleting the user

After confirming access to the admin panel, I modified the URL to target the user deletion endpoint:

```http
http://127.1/%2561dmin/delete?username=carlos
```

I sent the request through Burp Suite.

The server successfully processed the request and deleted the user `carlos`.

## Result

**Lab successfully solved!**

## Key Takeaways

- Blacklist-based SSRF filters can be bypassed when they rely on incomplete hostname validation.
- `127.1` is an alternative representation of the loopback address `127.0.0.1`.
- Double URL encoding can help bypass filters that decode input an insufficient number of times.
-
