# PortSwigger Web Security Academy — Multistep Clickjacking

**Difficulty:** Practitioner  
**Category:** Clickjacking  
**Status:** ✅ Solved

## 1. Lab Objective

The objective of this lab was to exploit a multistep clickjacking vulnerability to delete a user's account.

The application protects the account-deletion functionality with a CSRF token and an additional confirmation step. The attack must trick the victim into clicking two different controls:

1. The **Delete account** button.
2. The confirmation button, such as **Yes** or **Confirm**.

To achieve this, two decoy elements are positioned over the corresponding controls inside an almost transparent iframe.

## 2. Vulnerability Analysis

Unlike a basic clickjacking attack, this lab requires multiple user interactions in a specific sequence.

The application has two relevant protections or behaviors:

- **CSRF token:** Protects the state-changing request against certain cross-site request forgery attacks. However, it does not automatically prevent clickjacking if the legitimate application page can be framed and the user can interact with its real controls.
- **Confirmation dialog:** Adds a second interaction before the account can be deleted. The attacker must account for this additional step.

The key insight is that the victim's clicks can be redirected through visual deception to legitimate controls within the framed application.

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
        top: 505px;
        left: 60px;
        z-index: 1;
        font-size: 20px;
        font-weight: bold;
    }

    .next-click {
        position: absolute;
        top: 300px;
        left: 210px;
        z-index: 1;
        font-size: 20px;
        font-weight: bold;
    }
</style>

<div class="wannabe-click">click first</div>
<div class="next-click">click next</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```

*Note: Replace `YOUR-LAB-ID` with the actual lab hostname.*

## 4. Exploitation Steps

1. Logged in to my own lab account using the provided credentials.
2. Identified the account-deletion functionality and its additional confirmation step.
3. Embedded the account page in an iframe.
4. Made the iframe almost completely transparent using the CSS `opacity` property.
5. Created the first decoy element, `click first`, and positioned it over the **Delete account** button.
6. Created the second decoy element, `click next`, and positioned it over the confirmation button.
7. Submitted the HTML to the exploit server and adjusted the coordinates to align the decoy elements with the actual controls.
8. Tested the exploit in Chrome and triggered both interactions in sequence.
9. Successfully completed the lab when the account-deletion action was confirmed.

**Result:** The lab was solved successfully.

## 5. Technical Explanation

### Why are two decoy elements necessary?

The application requires two separate clicks. The first initiates the account-deletion process, while the second confirms the action.

A single decoy would not be sufficient to guide both interactions. Therefore, the exploit uses two independently positioned elements.

### How does the iframe work?

The iframe loads the legitimate account page, including its real controls and CSRF token. Because the iframe is nearly invisible but remains interactive, the victim can unknowingly interact with the underlying application.

### What is the role of CSS positioning?

- `position: absolute` allows the decoy elements to be placed at specific coordinates.
- `top` and `left` control their positions.
- `z-index` establishes the stacking order, with the iframe placed above the decoy elements.
- `opacity` makes the iframe almost invisible.

The coordinates must be adjusted to match the actual positions of the buttons in the framed page.

### Why doesn't the CSRF token stop the attack?

The attack does not need to forge a request or guess the CSRF token. Instead, it tricks the victim into interacting with the legitimate application in their authenticated browser session.

A CSRF token is not, by itself, a defense against clickjacking.

## 6. Key Takeaways

- Multistep clickjacking requires the attacker to account for multiple interactions in the correct sequence.
- Confirmation dialogs can add another step without necessarily preventing UI redressing.
- CSRF protection and clickjacking protection address different attack vectors.
- An iframe can contain legitimate, authenticated controls that are manipulated through deceptive visual positioning.
- Accurate coordinate alignment and understanding the application's interaction flow are essential to this lab.

## 7. Conclusion

This was my first successfully completed Practitioner-level clickjacking lab on PortSwigger Web Security Academy.

Compared with the previous Apprentice labs, this challenge introduced a multistep interaction flow and demonstrated why CSRF tokens alone do not prevent clickjacking.

**Lab completed successfully!** 🚀
