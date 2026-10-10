# Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

**Difficulty:** Apprentice  
**Vulnerability:** Reflected Cross-Site Scripting (XSS)  
**Status:** Solved

## 1. Lab Description

This lab contains a Reflected Cross-Site Scripting (XSS) vulnerability in the search query tracking functionality. The application HTML-encodes angle brackets, preventing the direct injection of HTML tags such as `<script>`.

The objective is to escape the JavaScript string containing the reflected search query and execute JavaScript that calls the `alert()` function.

## 2. Steps to Solve

1. Open the lab and navigate to the search functionality.
2. Enter a search query and inspect how the application reflects it in the page source.
3. Observe that the input is placed inside a JavaScript string:

   ```html
   <script>
       var searchQuery = 'USER_INPUT';
   </script>
   ```

4. Notice that the input is enclosed in single quotes. Instead of injecting HTML tags, use a single quote to break out of the existing JavaScript string.
5. Enter the following payload in the search field:

   ```javascript
   hello'; javascript:alert(0) //
   ```

6. Submit the search query. The lab reports that it has been solved.

## 3. Technical Explanation

The application reflects the search input inside a JavaScript string. Although angle brackets are HTML-encoded, this does not automatically protect the input from JavaScript injection.

The important issue is the context in which the input is inserted: a single-quoted JavaScript string.

### Payload breakdown

- `hello` — provides an initial string value.
- `'` — closes the original JavaScript string.
- `;` — terminates the assignment statement.
- `javascript:alert(0)` — the attempted JavaScript expression in the submitted payload.
- `//` — comments out the remaining text on the line, if any.

**Important technical note:** `javascript:` is a URL scheme, not a standalone JavaScript statement. In an ordinary inline `<script>` block, the expression `javascript:alert(0)` is interpreted as a JavaScript label, not as a `javascript:` URL. Therefore, the payload as written would not normally execute `alert(0)` in the displayed context. If the lab was marked as solved, inspect the actual rendered source and execution context to determine what triggered the success.

A conventional payload for escaping this specific single-quoted string context is:

```javascript
';alert(0)//
```

This closes the string, executes `alert(0)`, and comments out the trailing quote.

## 4. Key Takeaways

- Reflected XSS occurs when user-supplied input is immediately reflected in a response and interpreted as executable code.
- HTML encoding of angle brackets does not prevent JavaScript-context injection.
- Always identify the exact context in which input is reflected: HTML text, an HTML attribute, a JavaScript string, or another context.
- Escaping a JavaScript string requires context-appropriate output encoding; input validation alone is not a complete defense.

## 5. Conclusion

This lab demonstrates why output encoding must match the context in which user input is inserted. The search query was reflected inside a single-quoted JavaScript string, creating a potential opportunity to break out of the string and execute JavaScript.
