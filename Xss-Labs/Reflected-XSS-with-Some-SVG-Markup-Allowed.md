# Lab: Reflected XSS with Some SVG Markup Allowed

**Difficulty:** Practitioner

## Lab Description

This lab contains a simple reflected XSS vulnerability. The application blocks common HTML tags but allows certain SVG tags and event handlers.

The goal is to execute a cross-site scripting attack that calls the `alert()` function.

## Solution

### 1. Identifying Allowed HTML Tags

Testing every HTML tag manually would be time-consuming. Instead, I used the PortSwigger XSS Cheat Sheet to obtain a list of HTML and SVG tags.

I copied the tags and used Burp Suite Intruder to test which ones were not blocked by the application's filtering mechanism.

First, I intercepted the search request and sent it to Intruder. I selected the search parameter as the payload position and placed the payload marker between the angle brackets.

After starting the attack, I examined the results.

The following tags were allowed:

- `svg`
- `title`
- `image`
- `animatetransform`

### 2. Identifying Allowed Event Handlers

After identifying the allowed tags, I focused on finding an event handler that could trigger JavaScript execution.

I updated the search query to use the following structure:

```html
<svg><animatetransform §§=1>
```

I then used Burp Suite Intruder to test different event handlers.

After the attack finished, I found that `onbegin` was the only event handler that worked with the tested payloads.

### 3. Constructing the XSS Payload

I constructed the following payload:

```html
<svg><animatetransform onbegin=alert(0)>
```

The `onbegin` event handler can execute JavaScript when the SVG animation begins.

### 4. Solving the Lab

Finally, I entered the payload into the website's search field.

**Result:** The JavaScript alert was triggered, and the lab was successfully solved.

## Key Takeaways

- Burp Suite Intruder can automate testing of HTML tags and event handlers.
- The PortSwigger XSS Cheat Sheet is useful for identifying potential XSS vectors.
- SVG elements and their event handlers can provide alternative XSS vectors when common HTML tags are blocked.
- Filtering common HTML tags does not necessarily prevent XSS. Proper output encoding and secure handling of user input are essential.

## AI Usage

AI assistance was used to help organize and document the lab-solving process. The payload discovery and testing approach are described based on the lab exercise.
