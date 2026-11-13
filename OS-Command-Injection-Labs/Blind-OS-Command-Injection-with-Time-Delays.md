# Lab: Blind OS Command Injection with Time Delays

**Difficulty:** Practitioner  
**Category:** OS Command Injection  
**Platform:** PortSwigger Web Security Academy  
**Status:** Solved

## Objective

The application contains a blind OS command injection vulnerability in its feedback functionality.

The output of injected commands is not returned in the HTTP response.

To solve the lab, I needed to exploit the vulnerability by executing a command that causes a 10-second delay.

## 1. Intercepting the Request

I navigated to the feedback page and submitted a feedback form while intercepting the request using Burp Suite.

The intercepted request was:

```http
POST /feedback/submit HTTP/2
```

The relevant parameters were:

```http
csrf=...&name=s&email=se%40gma&subject=s&message=s
```

## 2. Injecting a Time-Delay Command

Since the application does not return command output, I decided to use a time-based technique.

I used the `ping` command to introduce a delay.

My initial idea was to send ICMP packets to Google's public DNS server (`8.8.8.8`).

I modified the `email` parameter as follows:

```http
email=s@gm||ping+-c+10+8.8.8.8||
```

The injected command was:

```bash
ping -c 10 8.8.8.8
```

The `-c 10` option instructs ping to send 10 packets.

Each packet normally takes approximately one second between transmissions, resulting in an approximately 10-second delay.

I sent the modified request using Burp Suite Repeater.

## 3. Observing the Delay

After sending the request, I noticed that the server response was delayed.

This indicated that the injected command was being executed by the server.

The delay provided evidence of successful blind OS command injection, even though the command output was not directly visible.

## 4. Result

The application experienced the expected delay, and PortSwigger confirmed that the lab was successfully solved.

**Lab successfully solved!**

## Key Takeaways

- Blind OS command injection does not necessarily return command output.
- Time-based techniques can help identify command execution through response delays.
- The `ping` command can introduce delays because it sends packets at intervals.
- Response-time differences can provide evidence of successful command execution.
- Always consider network conditions and baseline response times when interpreting delays.
