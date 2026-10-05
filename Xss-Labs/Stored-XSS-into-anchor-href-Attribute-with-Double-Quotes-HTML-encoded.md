# Stored XSS into anchor `href` Attribute with Double Quotes HTML-encoded

## Lab

**PortSwigger Web Security Academy**

**Lab:** Stored XSS into anchor `href` attribute with double quotes HTML-encoded

## Goal

Use the Stored XSS vulnerability in the comment functionality to trigger `alert()` when the comment author's name is clicked.

## 1. Finding the Author Name Context

After submitting a comment, I examined how the author name was rendered on the page.

The value I submitted was placed inside the `href` attribute of an anchor element, like this:

```html
<a id="author" href="dadafgadsadsg.com">selam</a>
```

Here I saw that the user input was placed directly into the value of the `href` attribute.

So the attack point was:

```text
Comment author name
        ↓
<a href="...">
        ↓
href attribute
```

## 2. Considering the HTML Encoding

The lab description noted that double quote (`"`) characters were HTML-encoded.

Because of this, trying to break out of the existing attribute, as in the previous attribute-context lab, with something like:

```text
" onfocus=...
```

wasn't viable here.

Using a single quote wouldn't work either, since the `href` attribute was defined using double quotes.

## 3. Using the `javascript:` URL Scheme

Once I saw that the user input went directly into the `href` value, instead of breaking out of the attribute I considered using the `href` value itself as a JavaScript URL.

I entered the following value into the author name field:

```text
javascript:alert(1)
```

As a result, the HTML roughly became:

```html
<a id="author" href="javascript:alert(1)">selam</a>
```

## 4. Triggering the XSS

After the comment was published, I clicked on the comment author's name.

Since the `href` value used the `javascript:` scheme:

```javascript
alert(1)
```

ran, and the lab was completed.

## Solution Summary

The vulnerability stemmed from the user-controlled author name value being placed directly into the `href` attribute of an anchor element.

Payload I used:

```text
javascript:alert(1)
```

Attack flow:

```text
Comment
   ↓
Author name
   ↓
<a href="...">
   ↓
javascript: URL
   ↓
alert(1)
```

Since this lab was Stored XSS, the payload was saved along with the comment and later triggered when the author name was clicked.

## What I learned

* I saw in practice the difference between Stored XSS and Reflected XSS.
* I learned that the HTML context matters in XSS.
* I identified that the user input was placed into the `href` attribute of the `<a>` element.
* I saw that, because double quotes were HTML-encoded, the classic attribute breakout method wasn't viable.
* I learned that the `javascript:` URL scheme can be used in the `href` attribute to execute JavaScript.
* I learned that in XSS, it's not always necessary to close a tag and create a new HTML element.

## AI Usage

**AI was not used in solving this lab.**
