# SQL Injection UNION Attack — Finding a Column Containing Text

## Lab

**PortSwigger Web Security Academy**

**Lab:** SQL injection UNION attack, finding a column containing text

## Goal

The goal of this lab was to determine which of the columns returned by the SQL query accepts string data and is displayed in the response, and then display a given string value in that column.

## 1. Finding the number of columns

First, I determined how many columns the query returns using `ORDER BY`.

The payload I used:

```sql
ORDER BY 3--
```

Since the payload worked, I determined that the query returns 3 columns.

## 2. Finding the column reflected in the response

Next, I sent a different value for each column to test which one was displayed in the response:

```sql
UNION SELECT '1', '2', '3'--
```

When I examined the response, I saw that the value `2` was displayed.

This told me that the second column was both accepted by the query and reflected in the application's response.

## 3. Displaying the given string

Now that I knew the target column was the second one, I placed the given string `So7xek` into this column.

The payload I used:

```sql
UNION SELECT NULL, 'So7xek', NULL--
```

When I sent the payload, the string `So7xek` was displayed in the response.

The lab was completed successfully.

## Solution Summary

```text
ORDER BY 3
    ↓
Determine there are 3 columns
    ↓
UNION SELECT '1', '2', '3'
    ↓
Determine the 2nd column is reflected in the response
    ↓
UNION SELECT NULL, 'So7xek', NULL
    ↓
Display the So7xek string
    ↓
Lab completed
```

## What I learned

* Determining the number of columns using `ORDER BY`
* Testing whether columns are reflected in the response using `UNION SELECT`
* Using string values to identify the reflected column
* Placing a desired string value into a specific column

## AI Usage

**AI was not used in solving this lab.**
