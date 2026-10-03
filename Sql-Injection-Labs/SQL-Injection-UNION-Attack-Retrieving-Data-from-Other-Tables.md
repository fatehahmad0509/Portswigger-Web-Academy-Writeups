# SQL Injection UNION Attack — Retrieving Data from Other Tables

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection UNION attack, retrieving data from other tables

## Goal

The goal of this lab was to use SQL injection to retrieve data from another table, and log into the `administrator` account by obtaining their password.

## 1. Finding the number of columns

First, I determined how many columns the query returns using `ORDER BY`.

The payload I used:

```sql
ORDER BY 2--
```

Since the payload worked, I determined that the query returns 2 columns.

## 2. Finding the reflected columns

Next, I sent different values for both columns to test which ones were displayed in the response:

```sql
UNION SELECT '1', '2'--
```

When I examined the response, I saw that both the `1` and `2` values were displayed.

This told me that both columns were reflected in the response.

## 3. Extracting the administrator's password

Since I knew the lab's target table was `users` and that this table contained the `username` and `password` columns, I directly queried for the password belonging to the `administrator` user:

```sql
UNION SELECT username, password
FROM users
WHERE username = 'administrator'--
```

The query ran successfully, and the `administrator` user's password was displayed in the response.

I logged in through the login page using the credentials I obtained.

The login was successful and the lab was completed.

## Solution Summary

```text
ORDER BY 2
    ↓
Determine there are 2 columns
    ↓
UNION SELECT '1', '2'
    ↓
Determine both columns are reflected in the response
    ↓
Query the administrator row from the users table
    ↓
Extract the username + password values
    ↓
Log into the administrator account
    ↓
Lab completed
```

## What I learned

* Determining the number of columns using `ORDER BY`
* Identifying which columns are reflected in the response using `UNION SELECT`
* Retrieving data from another table through SQL injection
* Filtering data for a specific user using a `WHERE` condition
* That SQL injection can return not just the current query's data, but also data from other tables under the right conditions

## AI Usage

**AI was not used in solving this lab.**
