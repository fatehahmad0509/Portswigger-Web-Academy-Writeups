# SQL Injection UNION Attack — Determining the Number of Columns Returned by the Query

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection UNION attack, determining the number of columns returned by the query

## Goal

The goal of this lab was to determine how many columns the application's SQL query returns, and then use `UNION SELECT` to return the same number of `NULL` values.

## 1. Finding the number of columns

First, I tried to determine how many columns the query returns using `ORDER BY`.

The payload I used:

```sql
ORDER BY 3--
```

Since this payload worked, I determined that the query returns 3 columns.

## 2. Verifying the column count with UNION SELECT

After determining the column count, I returned the same number of `NULL` values using `UNION SELECT`:

```sql
UNION SELECT NULL, NULL, NULL--
```

The query ran successfully and the lab was completed.

## Solution Summary

```text
ORDER BY 3
    ↓
Determine there are 3 columns
    ↓
UNION SELECT NULL, NULL, NULL
    ↓
Lab completed
```

## What I learned

* Determining the number of columns in a query using `ORDER BY`
* The need for matching column counts when using `UNION SELECT`
* That `NULL` values can be used to verify the column count

## AI Usage

**AI was not used in solving this lab.**
