# PortSwigger Lab: OS Command Injection – Simple Case

**Difficulty:** Apprentice  
**Category:** OS Command Injection  
**Status:** Solved ✅

## 1. Lab Description

This lab contains an OS Command Injection vulnerability in the application's product stock checker.

The application inserts user-supplied `productId` and `storeId` parameters into an operating system command and executes it. It also returns the raw output of the executed command in the HTTP response.

**Lab objective:** Execute the `whoami` command to determine the name of the current operating system user.

## 2. Intercepting the Request

First, I performed a stock check on the product page and intercepted the resulting request using Burp Suite.

Initial HTTP request:

```http
POST /product/stock HTTP/2
Host: 0a21008f033d2900805f94b6002a0027.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

productId=1&storeId=1
```

The request contains two parameters:

- `productId`: The product ID.
- `storeId`: The store ID.

## 3. Vulnerability Testing

I first wanted to test whether the `storeId` parameter was being included in an operating system command.

To do this, I appended the `|` character followed by the `whoami` command to the parameter.

Modified request:

```http
POST /product/stock HTTP/2
Host: 0a21008f033d2900805f94b6002a0027.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

productId=1&storeId=1|whoami
```

The `|` character is a pipe operator in shell environments. It passes the output of the preceding command to the next command, allowing `whoami` to execute.

## 4. Response Analysis

The server returned the following HTTP response:

```http
HTTP/2 200 OK
Content-Type: text/plain; charset=utf-8
Content-Length: 13

peter-veIl26
```

I received `peter-veIl26` in the response, which was the output of the `whoami` command.

This confirmed that the command had been executed on the server and revealed that the current operating system user was `peter-veIl26`.

## 5. Conclusion

By appending a shell command separator to the `storeId` parameter, I was able to interfere with the command executed by the application.

I successfully confirmed the OS Command Injection vulnerability by executing `whoami` and retrieving its output from the HTTP response.

<badge color="success">Lab Solved</badge>

**What I learned:**
- The fundamentals of OS Command Injection.
- How shell metacharacters affect command execution.
- HTTP request manipulation using Burp Suite.
- How to identify the current operating system user using `whoami`.
- How returning raw command output makes vulnerability verification easier.
