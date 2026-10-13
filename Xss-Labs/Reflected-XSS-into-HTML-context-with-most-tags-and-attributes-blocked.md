# Lab: Reflected XSS into HTML context with most tags and attributes blocked


**Difficulty:** Practitioner  
**Vulnerability:** Reflected Cross-Site Scripting (XSS)  
**Status:** Solved

## Lab Description

This lab contains a Reflected XSS vulnerability in the search functionality. However, the application uses a Web Application Firewall (WAF) to block common XSS attacks.

To solve the lab, the WAF protection must be bypassed to execute the `print()` function without requiring any user interaction.

## Solution Steps

### 1. Finding Allowed HTML Tags

Testing every HTML tag manually would take too much time, so I used the list of tags available on PortSwigger's XSS Cheat Sheet.

I intercepted the search request using Burp Suite and sent it to Intruder. I configured a payload position in the search parameter to test multiple HTML tags automatically.

After reviewing the attack results, I found that most tags were blocked, but the `<body>` tag was allowed through the filter.

### 2. Finding Allowed Event Handlers

Next, I performed another Intruder attack to identify which event handlers could be used with the `<body>` tag.

I modified the search parameter to accommodate the `<body ...>` structure and tested a list of event handlers.

After the scan finished, I found several successful HTTP responses. Among the results, the `onresize` event handler caught my attention.

The `onresize` event can be triggered when a window or viewport changes size. This suggested that it might be possible to trigger the event without requiring any action from the user.

### 3. Finding a Trigger That Requires No User Interaction

The important requirement was that manually triggering `print()` in my own browser would not be sufficient. The exploit had to execute automatically when the target visited the exploit page.

At this stage, I used AI to help me explore the idea of changing an iframe's width after it loads to trigger the `onresize` event.

I then incorporated this approach into my payload so that the iframe's dimensions would change automatically after loading.

### 4. Preparing the Exploit and Solving the Lab

I prepared the exploit using an iframe containing the XSS payload. Changing the iframe's width after it loaded triggered the `onresize` event automatically.

Finally, I submitted the exploit through PortSwigger's Exploit Server and checked the lab's status.

**Result: Lab successfully solved!**

## What I Learned

- Systematically testing HTML tags and event handlers is useful when bypassing WAF filters.
- Burp Suite Intruder can automate the testing of multiple payloads and help compare the results.
- A successful HTTP response does not necessarily mean that a payload executed; results must be analyzed in context.
- Event handlers such as `onresize` can be triggered without user interaction under the right conditions.
- When testing XSS vulnerabilities, it is important to understand not only whether a payload can be injected, but also how it executes in the browser and whether it satisfies the lab's requirements.

## AI Usage

I performed the main steps of the solution myself. I only used AI for the idea of automatically triggering the `onresize` event by changing an iframe's dimensions after it loads.

I actively worked on running the tag and event-handler scans, analyzing the results, identifying a suitable event handler, and completing the exploit to solve the lab.
