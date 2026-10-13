# Lab: Reflected XSS into HTML context with all tags blocked except custom ones

## Lab Description

In this lab, almost all HTML tags are blocked, with only custom tags being allowed. The goal is to bypass this filter by injecting a custom HTML tag and automatically displaying the `document.cookie` value using the `alert()` function.

## Solution Steps

### 1. Finding Unblocked HTML Tags

Testing every HTML tag individually would take too much time, so I used PortSwigger's XSS Cheat Sheet. After copying the HTML tags from the page, I found the search request in Burp Suite and sent it to **Intruder**.

I added `<>` to the search parameter and placed a payload position between the two characters. Then, I added the copied HTML tags to the payload list and started the attack.

The scan showed that most tags were blocked, but the following four tags were allowed through the filter:

- `animate`
- `animatemotion`
- `animatetransform`
- `set`

### 2. Creating the XSS Payload

After identifying the unblocked tags, I investigated how they could be used. I first tried to figure it out myself and then worked with AI to develop the following payload:

```html
<xss id=x onfocus=alert(document.cookie) tabindex=1>
```

Here's what each part does:

- `id=x` assigns an ID to the injected element.
- `onfocus` is intended to execute JavaScript when the element receives focus.
- `alert(document.cookie)` displays the cookie value in an alert box.
- `tabindex=1` allows the element to receive keyboard focus.

I placed the payload in the search parameter and tested the resulting URL in my browser. **The payload worked successfully.** However, to complete the lab, the attack also needed to trigger automatically in the victim's browser.

### 3. Attempting the Attack Through the Exploit Server

First, I tried using the following JavaScript code on the Exploit Server:

```html
<script>
    location = 'https://0ab600e4049c998f800712a900ec0092.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29+tabindex=1%3E#x';
</script>
```

I saved the code and clicked **Send to victim**, but the lab was not completed.

Next, I tried an alternative approach using an `iframe`:

```html
<iframe src="https://0ab600e4049c998f800712a900ec0092.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%281%29+tabindex=1%3E#x"></iframe>
```

This approach did not work either.

### 4. Identifying the Error and Completing the Lab

While reviewing the original JavaScript code, I noticed an extra, unusual character before the `location` statement. I suspected that this character, along with some unnecessary whitespace, might be breaking the code.

I corrected the code and tried again. This time, the attack succeeded, and **I completed the lab successfully.**

## What I Learned

- I learned how to systematically test multiple HTML tags using Burp Suite Intruder.
- I understood how identifying which tags are allowed through an XSS filter helps with developing a suitable payload.
- I learned how `onfocus` and `tabindex` can be used in XSS scenarios.
- I realized that a payload working in my own browser does not necessarily mean it will execute automatically in a victim's browser.
- I learned the importance of carefully checking JavaScript syntax, URL encoding, and unexpected characters when launching an attack through the Exploit Server.

**Conclusion:** By using the PortSwigger XSS Cheat Sheet, Burp Suite Intruder, and the Exploit Server, I identified the HTML tags that were not blocked, developed an appropriate XSS payload, and successfully completed the lab after correcting the malformed code.
