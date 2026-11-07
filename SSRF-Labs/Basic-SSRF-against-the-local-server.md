# Lab: Basic SSRF against the local server

**Platform:** PortSwigger Web Security Academy  
**Difficulty:** Apprentice  
**Vulnerability:** Server-Side Request Forgery (SSRF)  
**Status:** Solved ✅

## 1. Lab Description

This lab contains a stock check feature that fetches data from an internal system using a user-controlled URL.

The objective is to exploit the SSRF vulnerability to access the internal admin panel and delete the user `carlos`.

## 2. Request Analysis

First, I intercepted the stock check request using Burp Suite.

Relevant HTTP request:

```http
POST /product/stock HTTP/2
Host: 0ade004004baf1cc823bf14a00f9003b.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
```

I noticed the `stockApi` parameter, which controls the URL that the server fetches.

## 3. Exploitation – Accessing the Admin Panel

I replaced the original stock API URL with:

```text
http://localhost/admin
```

Then, I sent the modified request.

The server made a request to its own internal admin panel and returned the HTML content of the page.

This confirmed that I could access the internal admin interface through SSRF.

## 4. Identifying the Delete Endpoint

I inspected the HTML response and found the list of users in the admin panel.

The delete link for the `carlos` user was:

```html
<a href="/admin/delete?username=carlos">Delete</a>
```

This revealed the endpoint and parameter used to delete the user.

## 5. Exploitation – Deleting Carlos

I modified the `stockApi` parameter again, this time using the delete endpoint:

```text
http://localhost/admin/delete?username=carlos
```

After sending the request, the server accessed the internal endpoint and executed the deletion.

The operation was successful, and the lab status changed to **Solved**.

## 6. What I Learned

- SSRF occurs when a server makes requests to URLs controlled by user input.
- `localhost` refers to the machine on which the application is running.
- SSRF can be used to access internal services that are not directly accessible from the internet.
- Inspecting HTTP responses and HTML content can reveal additional application endpoints.
- SSRF can potentially allow attackers to perform actions on internal systems, not just retrieve information.

## 7. Key Takeaway

The most important lesson from this lab was understanding how a user-controlled URL parameter can lead to an SSRF vulnerability.

I also learned the importance of analyzing server responses to discover internal endpoints and determine the next steps during exploitation.
