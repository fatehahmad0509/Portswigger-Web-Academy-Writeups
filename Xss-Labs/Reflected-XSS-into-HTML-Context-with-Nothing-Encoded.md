# Reflected XSS into HTML Context with Nothing Encoded

## Lab

**PortSwigger Web Security Academy**

**Lab:** Reflected XSS into HTML context with nothing encoded

In this lab, user input entered into the search box is reflected into the HTML without any encoding applied.

The goal is to use this behavior to perform a Reflected XSS attack and trigger a JavaScript `alert` on the page.

## Goal

Trigger an `alert` on the page by submitting an XSS payload into the search box.

## 1. Trying the XSS Payload

I entered the following payload into the search box:

```html
<script>alert(1)</script>
```

Since the application reflects this input directly into the HTML without applying any encoding, the browser interpreted it not as plain text, but as a `<script>` element within the HTML.

As a result, the JavaScript ran and the:

```text
alert(1)
```

popup was displayed.

The lab was completed successfully.

## Solution Summary

```text
Search input
     ↓
<script>alert(1)</script>
     ↓
Input reflected into HTML without encoding
     ↓
Browser interprets the <script> tag
     ↓
JavaScript executes
     ↓
alert(1)
```

## What I learned

* I saw the logic of Reflected XSS applied hands-on.
* I learned why directly reflecting user input into HTML can be dangerous.
* I saw that when HTML encoding isn't applied, HTML elements like `<script>` can be interpreted by the browser.
* I saw that one of the key points in XSS, before even thinking about the payload, is understanding in which context and how the application processes user input.

## AI Usage

**AI was not used in solving this lab.**
