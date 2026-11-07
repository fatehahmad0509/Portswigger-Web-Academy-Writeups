# Lab: Basic SSRF against another Back-end System

**Difficulty:** Apprentice  
**Status:** Solved

## Objective

The application contains a stock check feature that fetches data from an internal back-end system.

The objective is to exploit the SSRF vulnerability to scan the internal `192.168.0.X` IP range, discover an admin interface running on port `8080`, and delete the user `carlos`.

## Exploitation

### 1. Intercepting the stock check request

First, I intercepted the stock check request using Burp Suite Proxy.

The request contained the following parameter:

```http
POST /product/stock HTTP/2
Host: 0a4200f5047898b1825801c9001c004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

The `stockApi` parameter controls the URL used by the server to retrieve stock information.

Since the server makes the request on our behalf, this functionality can be abused to perform Server-Side Request Forgery (SSRF).

### 2. Scanning the internal IP range

I sent the intercepted request to Burp Suite Intruder.

I modified the `stockApi` parameter to target the following URL:

```http
http://192.168.0.§X§:8080/admin
```

I configured the payload position to replace `X` with numbers ranging from `0` to `255`.

After launching the attack, most responses returned **HTTP 500 Internal Server Error**.

However, the request targeting `192.168.0.176` returned **HTTP 200 OK**.

This indicated that an accessible internal service was running at:

```http
http://192.168.0.176:8080
```

### 3. Discovering the admin interface

I examined the response returned by the discovered internal server.

The HTML source revealed an admin panel containing two users:

- `wiener`
- `carlos`

The admin panel also exposed the following deletion endpoint:

```http
/admin/delete?username=carlos
```

### 4. Deleting the user

I modified the `stockApi` parameter to make the internal server request the discovered deletion endpoint:

```http
http://192.168.0.176:8080/admin/delete?username=carlos
```

I sent the request through Burp Suite.

The request successfully deleted the user `carlos`.

## Result

<badge color="success">Lab Solved</badge>

The lab was successfully completed by exploiting an SSRF vulnerability in the stock check functionality.

## Key Takeaways

- SSRF occurs when an application fetches resources from URLs controlled or influenced by a user.
- Internal IP ranges can expose services that are not directly accessible from the outside.
- Different HTTP response codes can help identify accessible internal services.
- SSRF can potentially expose internal admin panels and allow unauthorized actions.
