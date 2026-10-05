# DOM XSS in `innerHTML` Sink using Source `location.search`


## Lab

**PortSwigger Web Security Academy**

**Lab:** DOM XSS in `innerHTML` sink using source `location.search`

## Goal

Use the DOM XSS vulnerability found in the search function to trigger `alert(1)`.

## 1. Finding the Source and Sink

While examining the page source, I saw the following JavaScript code:

```javascript
function doSearchQuery(query) {
    document.getElementById('searchMessage').innerHTML = query;
}

var query = (new URLSearchParams(window.location.search)).get('search');

if(query) {
    doSearchQuery(query);
}
```

Here, the user-controlled `search` parameter is taken from the URL:

```javascript
(new URLSearchParams(window.location.search)).get('search');
```

So the source is:

```text
location.search
```

This value is then passed into `innerHTML`:

```javascript
document.getElementById('searchMessage').innerHTML = query;
```

And the sink here is:

```text
innerHTML
```

The flow is as follows:

```text
URL search parameter
        ↓
location.search
        ↓
query
        ↓
innerHTML
        ↓
Interpreted as HTML
        ↓
XSS
```

## 2. The XSS Payload

I tried placing an HTML element into `innerHTML` that could execute JavaScript.

The payload I used:

```html
<img src=x onerror=alert(1)>
```

Here:

* `<img>` creates an HTML element.
* `src=x` sets an invalid source.
* When the source fails to load, the `onerror` event is triggered.
* `alert(1)` runs inside `onerror`.

When the payload was executed, `alert(1)` was displayed and the lab was completed.

## Solution Summary

The vulnerability stemmed from the user-controlled `location.search` data being passed directly into the `innerHTML` sink.

```text
location.search → innerHTML → XSS
```

Payload used:

```html
<img src=x onerror=alert(1)>
```

## What I learned

* The importance of the source and sink concepts in DOM XSS.
* That the `location.search` value can be controlled by the user.
* That using `innerHTML` can be risky in terms of HTML injection/XSS.
* That HTML event handlers (like `onerror`) can be used to execute JavaScript.
* That when finding an XSS vulnerability, it's important to examine how data flows from the source to the sink, rather than just trying payloads.

## AI Usage

**AI was not used in solving this lab.**
