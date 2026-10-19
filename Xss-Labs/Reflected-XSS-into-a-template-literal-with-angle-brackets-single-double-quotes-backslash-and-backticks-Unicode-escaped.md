# PortSwigger Write-up: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

Practitioner

Cross-Site Scripting (XSS)

## 1. Vulnerability

The application contains a reflected XSS vulnerability in its blog search functionality. The user-controlled search input is reflected inside a JavaScript template literal.

Although the application HTML-encodes angle brackets (`<`, `>`), single quotes (`'`) and double quotes (`"`), and escapes backticks, it fails to prevent JavaScript expressions from being evaluated inside the template literal.

## 2. Exploitation

I entered the following payload into the search field:

```
selam ${alert(0)}
```

## 3. Why it works

JavaScript template literals use backticks (`` ` ``) and support expression interpolation through `${...}`.

When the application inserts the search input into a template literal, the `${alert(0)}` expression is evaluated as JavaScript code. This allows the `alert()` function to execute without needing to inject HTML tags or escape the template literal using a backtick.

The payload works because the application does not properly neutralize template literal interpolation.

## 4. Result

The browser executed `alert(0)`, demonstrating that arbitrary JavaScript execution was possible in the vulnerable context. The lab was successfully solved.

## 5. Key takeaway

HTML encoding alone does not prevent XSS in JavaScript contexts. Output handling must account for the specific context in which user input is inserted. Template literals require particular care because `${...}` allows JavaScript expressions to be evaluated.
