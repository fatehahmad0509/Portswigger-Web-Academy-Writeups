# DOM XSS in `document.write` Sink Using Source `location.search`


## Lab

**PortSwigger Web Security Academy**

**Lab:** DOM XSS in `document.write` sink using source `location.search`

This lab contains a DOM-based XSS vulnerability.

In the application's search feature, the `location.search` value from the URL is picked up by JavaScript and passed into the `document.write()` function.

Because `document.write()` writes user-controlled data directly onto the page, XSS can be achieved.

## Goal

Use the search parameter to perform DOM XSS and trigger the JavaScript `alert` function.

## 1. Examining the Search Parameter

First, I typed the following into the search box:

```text
selammm
```

Then I examined the page source to check how the search value was used within the HTML.

The following section caught my attention:

```html
<h1>0 search results for 'selammm'</h1>
```

Here I saw that the search value was being written onto the page.

## 2. Executing the DOM XSS Payload

Using the context in which the search value appeared within the HTML, I broke out of the existing `<h1>` element and created a new HTML element.

The payload I used:

```html
"><svg onload=alert(1)>
```

When the payload ran, the `<svg>` element was created and the `onload` event was triggered, executing the JavaScript.

As a result, the:

```text
alert(1)
```

popup was displayed and the lab was completed.

## Solution Summary

```text
location.search
      ↓
document.write()
      ↓
User-controlled search parameter
      ↓
Written into the HTML context
      ↓
"><svg onload=alert(1)>
      ↓
onload event executes
      ↓
alert(1)
```

## What I learned

* I saw the key difference between DOM-based XSS and Reflected/Stored XSS.
* I learned that sources controllable through the URL, like `location.search`, can be used as a source in DOM XSS.
* I saw that the `document.write()` function writing user-controlled data directly into HTML can be dangerous.
* I saw that understanding the HTML context the payload lands in is important for the XSS payload to actually execute.
* I saw that JavaScript can also be executed through event handlers, without needing to use a `<script>` tag.

## AI Usage

**AI was not used in solving this lab.**
