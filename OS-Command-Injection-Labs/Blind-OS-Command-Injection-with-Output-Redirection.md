# Lab: Blind OS Command Injection with Output Redirection

**Difficulty:** Practitioner  
**Category:** OS Command Injection  
**Platform:** PortSwigger Web Security Academy  
**Status:** Solved

## Objective

The application contains a blind OS command injection vulnerability in its feedback functionality.

The output of injected commands is not directly displayed in the HTTP response. However, the application allows us to redirect command output to a writable directory:

`/var/www/images/`

This directory is used to serve product images. By redirecting command output to a file in this directory, we can retrieve the output through the application's image-loading endpoint.

The objective is to execute the `whoami` command and retrieve its output.

## 1. Intercepting the Request

I navigated to the feedback page and submitted a feedback form while intercepting the request using Burp Suite.

The intercepted request was:

```http
POST /feedback/submit HTTP/2
```

The request contained the following parameters:

```http
csrf=...&name=se&email=se%40gma&subject=s&message=s
```

## 2. Injecting the Command

Since the command output is not returned directly in the HTTP response, I used output redirection to save it into a file.

I modified the `name` parameter:

```http
name=||whoami>/var/www/images/output.txt||
```

The injected command was:

```bash
whoami > /var/www/images/output.txt
```

The `>` operator redirects the command's output into `output.txt`.

I sent the modified request using Burp Suite Repeater.

## 3. Retrieving the Command Output

After sending the request, I navigated to the application's image endpoint.

An example product image URL was:

```text
/image?filename=8.jpg
```

I replaced the filename with the name of the file I had created:

```text
/image?filename=output.txt
```

The complete URL was:

```text
https://0aac00c804b976d581aa2a5600870065.web-security-academy.net/image?filename=output.txt
```

## 4. Result

The application returned the contents of `output.txt`:

```text
peter-gPlxZX
```

This confirmed that the `whoami` command had executed successfully and its output had been retrieved.

**Lab successfully solved!**

## Key Takeaways

- Blind OS command injection occurs when injected commands execute but their output is not directly reflected in the HTTP response.
- Output redirection (`>`) can write command output into a file.
- A writable directory accessible through the web application can provide an alternative way to retrieve command output.
- Applications should never pass unsanitized user input into operating system commands.

