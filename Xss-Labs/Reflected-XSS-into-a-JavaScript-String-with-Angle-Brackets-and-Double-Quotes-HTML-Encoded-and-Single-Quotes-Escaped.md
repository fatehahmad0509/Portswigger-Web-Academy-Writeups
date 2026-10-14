# Lab: Reflected XSS into a JavaScript String with Angle Brackets and Double Quotes HTML-Encoded and Single Quotes Escaped

**Difficulty:** Practitioner  
**Vulnerability:** Reflected Cross-Site Scripting (XSS)

## 1. Analysis

First, I entered `selam` into the search bar to inspect how the input was reflected in the page's JavaScript code.

The resulting code was:

```javascript
var searchTerms = 'selam';
```

The input was reflected inside a JavaScript string enclosed in single quotes. Since angle brackets and double quotes were HTML-encoded and single quotes were escaped, injecting a standard XSS payload would not work directly.

I needed to find a way to escape the JavaScript string and execute JavaScript code.

## 2. Testing Backslash Escaping

I noticed that backslashes could affect how single quotes were interpreted. To investigate this behavior, I entered the following input:

```text
\' Selam
```

The resulting code was:

```javascript
var searchTerms = '\\' Selam';
```

This showed that the application was escaping the backslash, which changed how the single quote was interpreted in the JavaScript string.

## 3. Exploitation

My first attempt was to use the following payload:

```javascript
\\'-alert(0)-'
```

However, this payload did not solve the lab.

I then tried a different approach using a line comment to prevent the remaining JavaScript code from interfering with the payload:

```javascript
\'-alert(0)//
```

This payload successfully escaped the JavaScript string, executed `alert(0)`, and commented out the remaining code.

**The lab was solved successfully.**

## 4. What I Learned

- Reflected XSS can occur inside a JavaScript string, even when HTML characters are encoded.
- Backslash escaping can affect how quotes are interpreted in JavaScript.
- Understanding the output context is essential when developing an XSS payload.
- JavaScript comments can help prevent the remaining code from causing syntax errors.
- Testing different payloads and carefully inspecting the reflected output helps identify the correct exploitation technique.
