# PortSwigger Web Security Academy — Clickjacking with Form Input Data Prefilled from a URL Parameter

**Difficulty:** Apprentice  
**Category:** Clickjacking  
**Status:** ✅ Solved

## 1. Lab Objective

The objective of this lab was to exploit a clickjacking vulnerability to change the victim's email address.

The application allows the email field on the account page to be prefilled through a URL parameter. By combining this behavior with a transparent iframe and a decoy button, the victim can be tricked into clicking the real **Update email** button without realizing it.

## 2. Vulnerability Analysis

The vulnerability relies on two key behaviors:

- **URL parameter prepopulation:** Visiting `/my-account?email=fa@com` causes the email input field to be prefilled with the supplied value.
- **UI redressing:** The account page can be embedded in an iframe. By making the iframe almost completely transparent and positioning a decoy element over the real interface, the attacker can disguise the actual button the victim is clicking.

The victim believes they are clicking the decoy text, but their click is actually directed at the underlying **Update email** button.

## 3. Exploit Payload

I created the following HTML on the PortSwigger exploit server:

```html
<style>
    iframe {
        position: relative;
        width: 500px;
        height: 700px;
        opacity: 0.00001;
        z-index: 2;
    }

    .wannabe-click {
        position: absolute;
        top: 505px;
        left: 80px;
        z-index: 1;
        font-size: 20px;
        font-weight: bold;
    }
</style>

<div class="wannabe-click">click</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=fa@com"></iframe>
```

*Note: Replace `YOUR-LAB-ID` with the actual lab hostname.*

## 4. Exploitation Steps

1. Logged in to my own lab account using the provided credentials.
2. Identified that the email field could be prefilled through the `email` URL parameter.
3. Created an iframe pointing to the account page with the desired email value.
4. Set the iframe's opacity to `0.00001`, making it practically invisible while keeping it interactive.
5. Positioned a decoy element labeled `click` over the expected location of the real **Update email** button.
6. Submitted the exploit through the exploit server and tested it in Chrome.
7. Triggered the real button through the decoy overlay, causing the email address to change.

**Result:** The lab was solved successfully.

## 5. Technical Explanation

### How does `opacity: 0.00001` help?

The iframe is almost entirely transparent, but it remains present and interactive. Its contents can still receive mouse clicks.

### Why is `z-index: 2` used?

The iframe has a higher stacking order than the decoy element, so it receives the click first. The decoy text appears to be the target, while the actual button inside the iframe is underneath it.

### Why is the URL parameter important?

Without prepopulation, the victim might have to enter an email address manually. By supplying the value in the URL, the attacker removes that extra step and only needs the victim to trigger the button.

## 6. Key Takeaways

- Clickjacking is a UI redressing attack that tricks users into interacting with a different interface than the one they believe they are using.
- An iframe can be used to embed a target page beneath deceptive interface elements.
- URL-controlled form prepopulation can make clickjacking attacks more practical by reducing the interactions required from the victim.
- CSS positioning, stacking order, and opacity are important concepts when constructing clickjacking demonstrations.

## 7. Conclusion

This was my first successfully completed clickjacking lab on PortSwigger Web Security Academy.

The exercise helped me understand how an attacker can combine URL parameter behavior, iframe embedding, and CSS positioning to manipulate a user's interaction with a web application.

**Lab completed successfully!** 🚀
