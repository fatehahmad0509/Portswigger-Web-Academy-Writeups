# PortSwigger Web Security Academy — Basic Clickjacking with CSRF Token Protection

**Difficulty:** Apprentice  
**Category:** Clickjacking  
**Status:** ✅ Solved

## 1. Lab Objective

The objective of this lab was to delete a user's account by exploiting a clickjacking vulnerability, despite the application protecting the delete-account functionality with a CSRF token.

The attack involves embedding the account page inside an iframe and disguising the real **Delete account** button beneath a decoy element displaying the word `click`.

## 2. Vulnerability Analysis

The application uses a CSRF token to protect the account-deletion action. However, CSRF protection and clickjacking protection address different security problems.

In this scenario, the attacker does not need to forge a request or obtain the CSRF token directly. Instead, the victim's authenticated browser loads the legitimate account page, and the victim is tricked into clicking its real delete button.

The attack relies on:

- **Iframe embedding:** Loading the account page within another webpage.
- **Visual deception:** Making the iframe almost completely transparent.
- **CSS positioning:** Aligning a visible decoy element with the real account-deletion button.
- **Authenticated interaction:** The victim's browser interacts with the application using the existing login session.

## 3. Exploit Payload

I created the following HTML on the PortSwigger exploit server:

```html
<style>
    iframe {
        position: relative;
        width: 500px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }

    .wannabe-click {
        position: absolute;
        top: 550px;
        left: 60px;
        z-index: 1;
        font-size: 20px;
        font-weight: bold;
    }
</style>

<div class="wannabe-click">click first</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```

*Note: Replace `YOUR-LAB-ID` with the actual lab hostname.*

## 4. Exploitation Steps

1. Logged in to my own lab account using the supplied credentials.
2. Identified the account page containing the delete-account button.
3. Created an iframe pointing to `/my-account`.
4. Used `opacity: 0.0001` to make the iframe almost invisible while keeping it interactive.
5. Added a decoy element displaying `click first`.
6. Positioned the decoy over the real **Delete account** button using CSS.
7. Submitted the payload to the exploit server.
8. Tested the exploit in Chrome and adjusted the positioning until the click reached the intended button.
9. Successfully deleted the account and completed the lab.

**Result:** The lab was solved successfully.

## 5. Technical Explanation

### Why does the iframe remain interactive?

The CSS `opacity` property controls an element's transparency, not whether it can receive pointer events. Therefore, an iframe with a very small opacity value can still receive clicks.

### Why are `z-index` and positioning important?

The iframe has `z-index: 2`, while the decoy element has `z-index: 1`. This places the iframe above the decoy in the stacking order, allowing clicks to reach the underlying application's real controls.

The `top` and `left` properties determine where the decoy appears, and the coordinates must be aligned with the actual button inside the iframe.

### Why doesn't the CSRF token prevent the attack?

The token protects against certain forged cross-site requests. Clickjacking works differently: the victim is tricked into interacting with the legitimate application page, which can submit its own valid request using the authenticated session and the appropriate token.

Consequently, CSRF protection alone is not sufficient to prevent clickjacking.

## 6. Key Takeaways

- Clickjacking exploits discrepancies between what users think they are clicking and what actually receives their clicks.
- A nearly transparent iframe can remain fully interactive.
- CSS positioning and stacking order are fundamental to constructing a clickjacking demonstration.
- CSRF tokens do not independently protect against UI redressing.
- Applications should implement appropriate framing restrictions, such as CSP `frame-ancestors` and, where applicable, `X-Frame-Options`.

## 7. Conclusion

This lab demonstrated how clickjacking can bypass the practical effectiveness of CSRF protections by manipulating user interaction rather than directly forging a request.

Successfully completing the lab reinforced the distinction between CSRF and clickjacking and provided additional practice with iframes, CSS positioning, and browser interaction.

**Lab completed successfully!** 🔐
