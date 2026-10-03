# Blind SQL Injection with Conditional Responses

## Lab

**PortSwigger Web Security Academy**

**Lab:** Blind SQL injection with conditional responses

This lab contains a **blind SQL injection** vulnerability. The application uses a cookie called `TrackingId` for analytics purposes, and includes this value in an SQL query.

The result of the SQL query is not shown directly in the response, and no SQL error message is returned either. However, if the query returns at least one row, the page additionally displays the message:

```text
Welcome back
```

Using this behavior, I extracted the `administrator` user's password character by character, and then logged into the administrator account.

## Goal

Using the blind SQL injection vulnerability:

1. Find the `administrator` user's password character by character
2. Log into the administrator account with the password I found

## 1. Using Blind SQL Injection Logic

First, I appended a conditional SQL query to the end of the `TrackingId` cookie.

The query I used:

```sql
' AND (SELECT SUBSTR(password, 1, 1) FROM users WHERE username='administrator')='a'--
```

The logic here:

* `SUBSTR(password, 1, 1)` → takes the **1st character** of the password.
* `WHERE username='administrator'` → targets the administrator user's password.
* `='a'` → checks whether the character found is `a`.
* If the condition is **true**, the `Welcome back` message appears in the response.
* If the condition is **false**, this message does not appear.

This lets us use the application's response as a **boolean channel**.

## 2. Automating with Burp Suite Intruder

Since trying each character one by one would take a lot of time, I automated this process using Burp Suite Intruder.

After sending the request to Intruder, I set the attack type to:

```text
Cluster bomb
```

I created two different payload positions.

### First Payload — Character Position

I placed the first payload on the position value inside `SUBSTR`:

```sql
SUBSTR(password, §1§, 1)
```

I set this as the **Numbers** payload type and ran it across the range:

```text
1 → 20
```

This checked every position from the 1st character to the 20th character of the password.

### Second Payload — Guessed Character

I placed the second payload where the character comparison happens:

```sql
='§a§'--
```

I set this as a **Simple list** and added the possible letter/number characters to the list.

This allowed Intruder to try every character at each position to find which one caused the `Welcome back` message to appear.

## 3. Launching the Intruder Attack

When I launched the attack, Intruder automatically began trying all the combinations.

For example, logically, checks were made as follows:

```text
1st character → a, b, c, d, ...
2nd character → a, b, c, d, ...
3rd character → a, b, c, d, ...
...
20th character → a, b, c, d, ...
```

As the attack progressed, I distinguished the responses containing the `Welcome back` message by looking at response lengths.

At first:

```text
5 characters found.
```

Then:

```text
9 characters found.
```

And eventually the last few characters were revealed as well.

## 4. Extracting the Password by Comparing Responses

Once the attack finished, I compared the response lengths.

The responses containing the `Welcome back` message indicated that the corresponding character guess was **correct**.

By combining the characters according to their positions, I obtained the administrator's password.

I'm not sharing the password here for security reasons.

I then used this password to log into the administrator account, and **the lab was completed.**

## Solution Summary

```text
TrackingId cookie
       ↓
Blind SQL Injection
       ↓
Selecting a character with SUBSTR()
       ↓
Testing the condition as true/false
       ↓
"Welcome back" → condition is true
       ↓
Burp Suite Intruder
       ↓
Finding characters by position
       ↓
Administrator password
       ↓
Logging into the administrator account
```

## What I learned

* I learned that even when the SQL result isn't shown directly in blind SQL injection, information can still be extracted from the application's behavior.
* I saw that I could check specific characters of a string using `SUBSTR()`.
* I learned that whether a condition is true or false can be detected through a small change in the response.
* I learned how Burp Suite Intruder can be used to automatically test a large number of combinations.
* I saw that the **Cluster bomb** attack type can be used to try combinations of multiple payload positions.
* I experienced that the core logic of blind SQL injection is actually simple, but it requires patience due to the large number of combinations involved.

## AI Usage

**AI was not used in solving this lab.**
