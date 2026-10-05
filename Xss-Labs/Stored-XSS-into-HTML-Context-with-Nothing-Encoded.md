# Stored XSS into HTML Context with Nothing Encoded

## Lab

**PortSwigger Web Security Academy**

**Lab:** Stored XSS into HTML context with nothing encoded

This lab contains a Stored XSS vulnerability in the comment feature on blog posts.

Content entered into the comment field is saved without any encoding applied, and is executed within the HTML whenever the blog post is viewed again.

## Goal

Store an XSS payload using the comment field, and trigger a JavaScript `alert` whenever the blog post is viewed.

## 1. Adding the XSS Payload to the Comment Field

I entered the following payload into the comment field:

```html
<script>alert(1)</script>
```

After I submitted the comment, the payload was saved by the application.

When I opened the blog post again, the `<script>` tag inside the comment was interpreted by the browser as HTML, and the JavaScript ran, displaying the:

```text
alert(1)
```

popup.

The lab was completed successfully.

## Solution Summary

```text
Comment field
     ↓
<script>alert(1)</script>
     ↓
Payload is saved on the server
     ↓
Blog post is viewed again
     ↓
Payload is interpreted as HTML
     ↓
JavaScript executes
     ↓
alert(1)
```

## What I learned

* I saw the key difference between Stored XSS and Reflected XSS applied hands-on.
* I learned that in Stored XSS, the attacker's input is saved by the application.
* The stored malicious content can run again every time the page is viewed.
* I saw once again that adding user input into HTML without encoding can lead to XSS.

## AI Usage

**AI was not used in solving this lab.**
