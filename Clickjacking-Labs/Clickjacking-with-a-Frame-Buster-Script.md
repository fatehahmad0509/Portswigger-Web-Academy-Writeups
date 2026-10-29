# PortSwigger Web Security Academy — Clickjacking with a Frame Buster Script

**Difficulty:** Apprentice  
**Category:** Clickjacking  
**Status:** ✅ Solved

## 1. Lab Objective

The objective of this lab was to bypass a frame-busting script and perform a clickjacking attack that changes the victim's email address.

The application attempts to prevent itself from being embedded in an iframe. The challenge is to work around this protection, disguise the account page, and trick the victim into clicking the real **Update email** button.

## 2. Vulnerability Analysis

This lab builds on the basic clickjacking technique by introducing a frame-busting script.

A frame buster is a client-side script designed to detect when a page is embedded in a frame and prevent the page from being displayed in that context.

The attack relies on three key behaviors:

- **Frame-buster bypass:** The iframe's `sandbox` attribute restricts the embedded page's capabilities. Omitting the `allow-scripts` permission prevents JavaScript in the framed page from executing, neutralizing a JavaScript-based frame-busting mechanism.
- **URL parameter prepopulation:** Supplying an `email` parameter in the account-page URL prepopulates the email input field.
- **UI redressing:** An almost transparent iframe is positioned above a decoy element so that the victim's click reaches the actual account-page button.

## 3. Exploit Payload

I created the following HTML on the PortSwigger exploit server:

```html
<style>
    iframe {
        position: relative;
        width: 500px;
        height: 700px;
        opacity: 0.0000001;
        z-index: 2;
    }

    .wannabe-click {
        position: absolute;
        top: 480px;
        left: 80px;
        z-index: 1;
        font-size: 20px;
        font-weight: bold;
    }
</style>

<div class="wannabe-click">click</div>

<iframe
    src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=fateh@g.com"
    sandbox="allow-forms">
</iframe>
```

*Note: Replace `YOUR-LAB-ID` with the actual lab hostname.*

## 4. Exploitation Steps

1. Logged in to my own lab account using the provided credentials.
2. Identified that the account page uses a frame-busting mechanism to prevent iframe embedding.
3. Added `sandbox="allow-forms"` to the iframe.
4. Restricted the embedded page's capabilities so that the JavaScript-based frame-busting script could not execute, while allowing form submissions.
5. Added the `email` URL parameter to prepopulate the email field.
6. Used CSS positioning and an almost transparent iframe to align the real **Update email** button with the decoy text.
7. Submitted the exploit to the exploit server and tested it in Chrome.
8. Triggered the underlying button, changing the email address and successfully completing the lab.

**Result:** The lab was solved successfully.

## 5. Technical Explanation

### What does `sandbox="allow-forms"` do?

The `sandbox` attribute applies restrictions to content inside an iframe.

By default, sandboxed content cannot execute scripts or submit forms. The `allow-forms` token permits form submissions, but it does not permit JavaScript execution.

Because the frame-busting mechanism in this lab relies on JavaScript, preventing the script from running allows the page to remain inside the iframe.

### Why is `allow-scripts` omitted?

Adding `allow-scripts` would permit JavaScript to execute inside the iframe, allowing the frame-busting script to run again.

Leaving it out is the key to bypassing this particular protection.

### Why is the email parameter useful?

The URL parameter prepopulates the email field, eliminating the need for the victim to type an address manually. The attack then requires only the intended button interaction.

### Why are opacity and z-index important?

- `opacity: 0.0000001` makes the iframe practically invisible while keeping it interactive.
- `z-index: 2` places the iframe above the decoy element.
- The decoy text is positioned where the real button is expected to appear.

As a result, the click reaches the actual interface rather than merely interacting with the visible decoy.

## 6. Key Takeaways

- Frame-busting scripts attempt to prevent clickjacking by stopping a page from being embedded.
- The iframe `sandbox` attribute can restrict scripts and other capabilities within embedded content.
- `sandbox="allow-forms"` permits form submissions without permitting scripts to execute.
- Disabling scripts can neutralize a JavaScript-based frame buster, but this technique does not bypass every iframe protection.
- Clickjacking can combine multiple web concepts: iframe behavior, CSS stacking, URL parameters, and browser security restrictions.

## 7. Conclusion

This was my second successfully completed clickjacking lab on PortSwigger Web Security Academy.

Compared with the basic clickjacking lab, this challenge introduced a frame-busting defense and demonstrated how iframe sandboxing can change the behavior of embedded content.

**Lab completed successfully!** 🚀
