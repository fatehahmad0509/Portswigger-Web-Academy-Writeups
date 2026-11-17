# Lab: User ID controlled by request parameter, with unpredictable user IDs

**Difficulty:** Apprentice  
**Vulnerability:** IDOR – Horizontal Privilege Escalation

## Steps

1. Logged in using the provided credentials:
   - Username: `wiener`
   - Password: `peter`

2. Inspected the account page but couldn't find Carlos's GUID.

3. Explored the application's blog section and examined the published posts.

4. Noticed that clicking on a post author's profile redirected to a URL containing their GUID in the `userId` parameter.

5. Found a blog post authored by `carlos` on the homepage and accessed his profile.

6. Retrieved Carlos's GUID:

   `a683685a-ae36-4af0-9048-bc7ffbf57600`

7. Used the discovered GUID in the account page URL:

   `/my-account?id=a683685a-ae36-4af0-9048-bc7ffbf57600`

8. Successfully accessed Carlos's account information and retrieved his API key.

9. Submitted the API key as the lab solution.

**Result:** Lab successfully solved.

## What I Learned

Unpredictable identifiers such as GUIDs do not prevent IDOR vulnerabilities on their own.

Even if an identifier is difficult to guess, it may be exposed elsewhere in the application.

In this lab, the blog section disclosed user GUIDs through author profile links, allowing access to another user's account information.
