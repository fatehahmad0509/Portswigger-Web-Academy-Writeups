# Reflected XSS into Attribute with Angle Brackets HTML-encoded

## Lab

**PortSwigger Web Security Academy**

**Lab:** Reflected XSS into attribute with angle brackets HTML-encoded

## Goal

Inject an HTML attribute using the reflected user input from the search blog functionality, and trigger `alert()`.

## 1. Finding the Input's Location in the HTML

I typed `selamlar` into the search field and examined the page's source code.

I saw that my input was reflected inside the `value` attribute of an `input` element, like this:

```html
<input type=text placeholder='Search the blog...' name=search value="selamlar">
```

The key point here was that the input sat inside an HTML tag, within the value of the `value` attribute.

The lab description also noted that angle brackets (`<` and `>`) were HTML-encoded. Because of this, instead of trying to open a new `<script>` tag, I focused on breaking out of the existing attribute context.

## 2. Breaking Out of the Attribute Context

First, I used a double quote to close the existing `value` attribute:

```text
selamlar"
```

This put me in a position where I could add my own attribute.

## 3. Trying an Event Handler

I tried using an event handler to execute JavaScript.

I first used `onmousedown`:

```text
selamlar" onmousedown=alert(1) value="
```

When I clicked the input, `alert(1)` ran.

However, the lab was not marked as Solved.

The reason was that the `onmousedown` event required the user to click on the input. The lab's hint also stated that the payload needed to run in the victim's browser.

## 4. Triggering It Without User Interaction

Because of this, I figured I needed to make the event fire automatically when the page loaded.

I used the `onfocus` event together with the `autofocus` attribute:

```text
selamlar" onfocus=alert(1) autofocus value="
```

Here:

* `"` → breaks out of the existing `value` attribute.
* `onfocus=alert(1)` → runs JavaScript when the element gains focus.
* `autofocus` → makes the input automatically gain focus when the page loads.
* `value="` → closes the attribute without breaking the rest of the HTML structure.

With this payload, `alert(1)` ran automatically and the lab was marked as Solved.

## Solution Summary

The vulnerability stemmed from the fact that, even though the user's search input was encoded and reflected inside an HTML attribute, it was still possible to break out of the attribute context.

The core attack logic:

```text
User input
    ↓
value="..."
    ↓
Break out of the attribute
    ↓
Add the onfocus event handler
    ↓
autofocus
    ↓
alert(1)
```

Payload I used:

```text
selamlar" onfocus=alert(1) autofocus value="
```

## What I learned

* In Reflected XSS, it's important to determine which HTML context the input lands in.
* Encoding the `<` and `>` characters doesn't always prevent XSS.
* In an HTML attribute context, it's possible to break out of the existing attribute and add a new one.
* There's a difference between events that require user interaction, like `onmousedown`, and behaviors that trigger automatically.
* HTML attributes like `autofocus` can be important for triggering an XSS payload without user interaction.
* Triggering `alert` in my own browser is not the same as satisfying the condition the lab expects.

## AI Usage

**AI was not used in solving this lab.**
