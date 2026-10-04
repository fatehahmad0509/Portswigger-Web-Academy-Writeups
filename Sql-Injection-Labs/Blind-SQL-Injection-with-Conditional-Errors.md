# Blind SQL Injection with Conditional Errors

## Lab

**PortSwigger Web Security Academy**

**Lab:** Blind SQL injection with conditional errors

This lab contains a **blind SQL injection** vulnerability. The application uses a cookie called `TrackingId` for analytics purposes, and includes this value in an SQL query.

The result of the SQL query is not shown in the response, and the response doesn't change based on whether the query returns rows or not. However, if the SQL query causes an error, the application returns a distinct error response.

Using this behavior, I extracted the `administrator` user's password character by character, and then logged into the administrator account.

## Goal

Using the blind SQL injection vulnerability:

1. Find the `administrator` user's password character by character
2. Log into the administrator account with the password I found

## 1. Using Conditional Error Logic

In this lab, I used a `CASE WHEN` structure to guess characters.

The logic is as follows:

* If the guessed character is **correct**, `CASE WHEN` → returns `1`.
* If the guessed character is **wrong** → the `1/0` operation is executed.
* Since `1/0` causes an error on the SQL side, the application returns a **500 Internal Server Error**.

The payload I used:

```sql
' AND 1=(CASE WHEN (SUBSTR((SELECT password FROM users WHERE username='administrator'), §1§, 1)='§a§') THEN 1 ELSE 1/0 END)--
```

Here:

```sql
SUBSTR((SELECT password FROM users WHERE username='administrator'), 1, 1)
```

takes a specific character of the administrator user's password.

`CASE WHEN` then checks whether this character matches the character I'm guessing.

## 2. Testing the Payload in Repeater

I appended the payload to the end of the `TrackingId` cookie and first tested it using Burp Suite Repeater.

Since an incorrect character guess should cause the SQL query to error out, I checked the response.

As expected, the application returned:

```text
500 Internal Server Error
```

This confirmed that the conditional error technique I used was working.

## 3. Extracting the Password with Burp Suite Intruder

Next, I sent the request to Burp Suite Intruder and, as in the previous blind SQL injection lab, used the **Cluster Bomb** attack type.

I used two payload positions:

```sql
SUBSTR(..., §1§, 1)
```

The first position determines the position of the character within the password.

The second position is the guessed character:

```sql
='§a§'
```

Intruder helped me determine which combination caused the SQL error by automatically trying different positions and character guesses.

Since this lab's method was similar to the previous one, I didn't go into detail about the Intruder configuration again here.

## 4. Finding the Password Character by Character

Once the attack started, I began extracting the characters in sequence.

At the first stage:

```text
4 characters found.
```

Then:

```text
10 characters found.
```

Once only a few characters remained, I waited for the attack to finish.

Once the attack completed, I combined the characters according to their positions and obtained the administrator's password.

I'm not sharing the password publicly here.

I then used the password I obtained to log into the administrator account, and **the lab was completed.**

## Solution Summary

```text
TrackingId cookie
       ↓
Blind SQL Injection
       ↓
Guessing a character with CASE WHEN
       ↓
Correct character → normal response
Wrong character → 1/0 → SQL error
       ↓
Burp Suite Intruder
       ↓
Extracting the password character by character
       ↓
Administrator password
       ↓
Logging into the administrator account
```

## What I learned

* I learned that blind SQL injection can be exploited not only through differences in response content, but also **through SQL errors**.
* I saw that the `CASE WHEN` structure can be used to build conditional SQL expressions.
* I reapplied the use of `SUBSTR()` to select a specific character of the password.
* I learned that by using an operation like `1/0` in a controlled way, an SQL error can be turned into an information channel.
* I was able to automate character guessing using Burp Suite Intruder.

## AI Usage

In this lab, I only used AI for help with **constructing the `CASE WHEN` syntax**.

Since I wasn't yet fully familiar with writing the `CASE WHEN` structure, I got help with this part of the payload. I implemented the blind SQL injection logic, the method of detecting characters through errors, and the use of Burp Suite Intruder myself.
