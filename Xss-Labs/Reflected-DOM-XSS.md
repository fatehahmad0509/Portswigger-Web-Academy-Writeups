# Lab: Reflected DOM XSS

**Difficulty:** Practitioner  
**Vulnerability:** Reflected DOM-based Cross-Site Scripting (XSS)  
**Status:** Solved ✅

## 1. Lab Description

This lab demonstrates a Reflected DOM XSS vulnerability in the search functionality. The goal is to create an injection that executes the `alert()` function.

Reflected DOM XSS occurs when a server response is processed unsafely by client-side JavaScript. In this lab, the dangerous operation involves the use of `eval()`.

## 2. Inspecting the JavaScript File

First, I investigated how the search functionality worked by examining the following JavaScript file:

`/resources/js/searchResults.js`

I found this line of code:

```javascript
eval('var searchResultsObj = ' + this.responseText);
```

Here, `this.responseText` contains the response returned by the server. The `eval()` function evaluates the resulting string as JavaScript code.

This suggested that if I could control part of the server response, I might be able to inject JavaScript into the evaluated expression.

## 3. Inspecting the Server Response

Using the browser's Developer Tools, I examined the response to a search request:

```json
{
  "results": [],
  "searchTerm": "selam"
}
```

I noticed that the search term was reflected in the `searchTerm` field. Since this response was processed by the `eval()` statement, I investigated whether the reflected input could be used for JavaScript injection.

## 4. First Payload Attempt

I initially entered the following payload into the search field:

```text
" alert(0) //
```

However, it did not work. After further investigation, I noticed that quotation marks were being escaped with backslashes.

This explained why my initial attempt to break out of the string context failed.

## 5. Successful Payload

After taking the escaping behavior into account, I modified the payload:

```text
\" -alert(0)- } //
```

This time, `alert(0)` executed successfully, and the lab was solved.

### How the Payload Works

- `\\` — Takes advantage of how backslashes are processed in the JavaScript string context.
- `"` — Targets the quotation mark delimiting the string in the evaluated expression.
- `-alert(0)-` — Executes `alert(0)` as part of a JavaScript expression.
- `}` — Attempts to break out of the surrounding object-like structure.
- `//` — Comments out the remaining characters where applicable.

The payload works because of the specific way the server response is constructed, how characters are escaped, and how the resulting string is interpreted by `eval()`.

## 6. Key Takeaways

- Reflected DOM XSS requires examining how client-side JavaScript processes server responses.
- Using `eval()` with untrusted input can introduce serious XSS vulnerabilities.
- Understanding quotation marks and backslash escaping is essential when analyzing JavaScript injection contexts.
- Tracing the flow of user input and identifying the relevant execution context can help explain why a payload succeeds or fails.

## Conclusion

I inspected
