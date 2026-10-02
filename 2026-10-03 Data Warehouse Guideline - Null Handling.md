# 2026-10-03 Data Warehouse Guideline - Null Handling

By [Vincentius Timothy](https://registry.jsonresume.org/vincentimo).

<!-- toc-gitlab:start mode=full -->
## Contents<br>
1. [Background](#background)
2. [What is an operator and an operand?](#what-is-an-operator-and-an-operand)
3. [`NULL` operand in mathematical, comparison, and string operations](#null-operand-in-mathematical-comparison-and-string-operations)
4. [`NULL` operand in boolean operations](#null-operand-in-boolean-operations)
5. [`NULL` values in aggregate functions](#null-values-in-aggregate-functions)
6. [`NULL` values in `ORDER BY`](#null-values-in-order-by)
7. [`NULL` values in `JOIN`](#null-values-in-join)
8. [`NULL` values in `NOT IN`](#null-values-in-not-in)
	1. [Why does the main query return 0 rows if the subquery returns a single `NULL` value?](#why-does-the-main-query-return-0-rows-if-the-subquery-returns-a-single-null-value)
	2. [Why does it work with left antijoin?](#why-does-it-work-with-left-antijoin)
<!-- toc-gitlab:end -->

## Background

`NULL` values in databases might have unexpected behavior. **This guideline describes pitfalls related to `NULL` values, and how to avoid them.** All queries here are tested on [PostgreSQL](https://www.postgresql.org/) 16.14.

## What is an operator and an operand?

An **operator** is a symbol or keyword to do a task, while an **operand** is the value or variable that the operator acts upon.

Example: Consider this math expression: `3 + 5`.

- Operator: `+`
- Operands: `3` and `5`

There are several types of operators.

| Type                | Description        | Example operator                                                   | Example expression    |
| ------------------- | ------------------ | ------------------------------------------------------------------ | --------------------- |
| 1. Unary operator   | Accepts 1 operand  | [Additive inverse](https://en.wikipedia.org/wiki/Additive_inverse) | `-5`                  |
| 2. Binary operator  | Accepts 2 operands | [Addition](https://en.wikipedia.org/wiki/Addition)                 | `3 + 5`               |
| 3. Ternary operator | Accepts 3 operands | [Between](https://www.w3schools.com/sqL/sql_between.asp)           | `4 BETWEEN 3 AND 5`   |
| 4. n-ary operator   | Accepts n operands | [Concatenation](https://en.wikipedia.org/wiki/Concatenation)       | `CONCAT('a', 'b', …)` |

## `NULL` operand in mathematical, comparison, and string operations

**In most cases**, [mathematical operations](https://www.postgresql.org/docs/16/functions-math.html), [comparison operations](https://www.postgresql.org/docs/16/functions-comparison.html), and [string operations](https://www.postgresql.org/docs/16/functions-string.html) with at least one `NULL` operand will return `NULL`. Examples:

```sql
SELECT
	/* Unary operators */
	+ NULL::NUMERIC AS unary_plus,
	- NULL::NUMERIC AS negation,
	|/ NULL::DOUBLE PRECISION AS square_root,
	||/ NULL::DOUBLE PRECISION AS cube_root,
	@ NULL::NUMERIC AS absolute_value,
	~ NULL::BIGINT AS bitwise_not,
	
	/* Binary operators */
	8 + NULL::NUMERIC AS addition,
	8 - NULL::NUMERIC AS subtraction,
	8 * NULL::NUMERIC AS multiplication,
	8 / NULL::NUMERIC AS division,
	8 % NULL::NUMERIC AS modulo_remainder,
	8 ^ NULL::NUMERIC AS exponentiation,
	8 & NULL::BIGINT AS bitwise_and,
	8 | NULL::BIGINT AS bitwise_or,
	8 # NULL::BIGINT AS bitwise_exclusive_or,
	8 << NULL::INT AS bitwise_shift_left,
	8 >> NULL::INT AS bitwise_shift_right,
	8 < NULL::NUMERIC AS less_than,
	8 > NULL::NUMERIC AS greater_than,
	8 <= NULL::NUMERIC AS less_than_or_equal,
	8 >= NULL::NUMERIC AS greater_than_or_equal,
	8 = NULL::NUMERIC AS equal,
	8 <> NULL::NUMERIC AS not_equal_1,
	8 != NULL::NUMERIC AS not_equal_2,
	
	/* Ternary operators */
	8 BETWEEN NULL::NUMERIC AND 10 AS between,
	8 NOT BETWEEN NULL::NUMERIC AND 10 AS not_between,
	
	/* n-ary operators */
	'a' || NULL::TEXT AS concat;
```

However, other operations with at least one `NULL` operand will **not** return `NULL`. Examples:

```sql
SELECT
	/* Unary operators */
	NULL::BOOLEAN IS TRUE AS is_true,                             /* Result: FALSE */
	NULL::BOOLEAN IS NOT TRUE AS is_not_true,                     /* Result: TRUE */
	NULL::BOOLEAN IS NULL AS is_null,                             /* Result: TRUE */
	NULL::BOOLEAN IS NOT NULL AS is_not_null,                     /* Result: FALSE */
	NULL::BOOLEAN IS FALSE AS is_false,                           /* Result: FALSE */
	NULL::BOOLEAN IS NOT FALSE AS is_not_false,                   /* Result: TRUE */
	NULL::BOOLEAN IS UNKNOWN AS is_unknown,                       /* Result: TRUE */
	NULL::BOOLEAN IS NOT UNKNOWN AS is_not_unknown,               /* Result: FALSE */
	
	/* Binary operators */
	8 IS DISTINCT FROM NULL::NUMERIC AS is_distinct_from,         /* Result: TRUE */
	8 IS NOT DISTINCT FROM NULL::NUMERIC AS is_not_distinct_from, /* Result: FALSE */
	  
	/* n-ary operators */
	CONCAT('a', NULL::TEXT) AS concat;                            /* Result: 'a' */
```

A common pitfall happens when filtering data with multiple conditions - when at least one field contains `NULL` value, you may want to recheck the filtering result. This is because filtering includes the rows with `TRUE` filter evaluation result, while excludes the rows with `NULL` or `FALSE` filter evaluation result.

| Filter evaluation result | Is included in the filtering result? |
| ------------------------ | ------------------------------------ |
| `TRUE`                   | ✅ Included                           |
| `NULL`                   | ❌ Excluded                           |
| `FALSE`                  | ❌ Excluded                           |

## `NULL` operand in boolean operations

[Boolean operations](https://www.postgresql.org/docs/16/functions-logical.html) will be evaluated as [three-valued logic](https://en.wikipedia.org/wiki/Three-valued_logic). Learn the truth table.

| a       | NOT a   |
| ------- | ------- |
| `TRUE`  | `FALSE` |
| `NULL`  | `NULL`  |
| `FALSE` | `TRUE`  |

| AND         | `TRUE`  | `NULL`  | `FALSE` |
| ----------- | ------- | ------- | ------- |
| **`TRUE`**  | `TRUE`  | `NULL`  | `FALSE` |
| **`NULL`**  | `NULL`  | `NULL`  | `FALSE` |
| **`FALSE`** | `FALSE` | `FALSE` | `FALSE` |

| OR          | `TRUE` | `NULL` | `FALSE` |
| ----------- | ------ | ------ | ------- |
| **`TRUE`**  | `TRUE` | `TRUE` | `TRUE`  |
| **`NULL`**  | `TRUE` | `NULL` | `NULL`  |
| **`FALSE`** | `TRUE` | `NULL` | `FALSE` |

## `NULL` values in aggregate functions

In most cases, [aggregate functions](https://www.postgresql.org/docs/16/functions-aggregate.html) will ignore `NULL`. However, other functions will not. Examples:

```sql
WITH
	get_transaction AS (
		SELECT 1 AS id, 1000 AS transaction_amt, 1 AS customer_id
		UNION ALL SELECT 2, 2000, NULL
		UNION ALL SELECT 3, NULL, 3
		UNION ALL SELECT 4, 4000, 3
	)
SELECT
	COUNT(1),                          /* Result: 4 - Doesn't ignore NULL */
	COUNT(customer_id),                /* Result: 3 - Ignores NULL */
	COUNT(DISTINCT customer_id),       /* Result: 2 - Ignores NULL */
	SUM(transaction_amt),              /* Result: 7000 - Ignores NULL */
	MIN(transaction_amt),              /* Result: 1000 - Ignores NULL */
	MAX(transaction_amt),              /* Result: 4000 - Ignores NULL */
	ARRAY_AGG(customer_id),            /* Result: {1, 3, 3, NULL} - Doesn't ignore NULL */
	STRING_AGG(customer_id::TEXT, ',') /* Result: '1,3,3' - Ignores NULL */
FROM
	get_transaction;
```

## `NULL` values in `ORDER BY`

A common pitfall happens when doing [window functions](https://www.postgresql.org/docs/16/functions-window.html) with `ORDER BY`. On PostgreSQL, `ORDER BY ASC` will put `NULL` values last, while `ORDER BY DESC` will put `NULL` values first, [unless stated otherwise](https://www.postgresql.org/docs/16/queries-order.html). **This principle might be different on other Database Engines.** Examples:

```sql
 WITH
	get_customer AS (
		SELECT 1 AS id, 'Alpha' AS name
		UNION ALL SELECT 2, NULL
		UNION ALL SELECT NULL, 'Gamma'
	)
SELECT
	ARRAY_AGG(id ORDER BY id ASC),     /* Result: {1, 2, NULL} */
	ARRAY_AGG(name ORDER BY name ASC), /* Result: {'Alpha', 'Gamma', NULL} */
	ARRAY_AGG(id ORDER BY id DESC),    /* Result: {NULL, 2, 1} */
	ARRAY_AGG(name ORDER BY name DESC) /* Result: {NULL, 'Gamma', 'Alpha'} */
FROM
	get_customer;
```

> [!TIP]
> Explicitly state `NULLS LAST` or `NULLS FIRST` on `ORDER BY` to ensure consistently expected result, instead of relying on the Database Engine's default behavior.

## `NULL` values in `JOIN`

This is a common pitfall - there's the principle.

| Join condition evaluation | Will join match?                                             |
| ------------------------- | ------------------------------------------------------------ |
| `TRUE`                    | ✅ Match                                                      |
| `NULL`                    | ⚠️ Match depending on inner, left, right, or full outer join |
| `FALSE`                   | ❌ Won't match                                                |

Example:

```sql
WITH
	get_transaction AS (
	    SELECT 1 AS id, 1000 AS transaction_amt, 1 AS customer_id
	    UNION ALL SELECT 2, 2000, NULL
	    UNION ALL SELECT 3, NULL, 3
	    UNION ALL SELECT 4, 4000, 3
	),
	get_customer AS (
	    SELECT 1 AS id, 'Alpha' AS name
	    UNION ALL SELECT 2, NULL
	    UNION ALL SELECT NULL, 'Gamma'
	)
SELECT
	get_transaction.id AS transaction_id,
	get_transaction.transaction_amt,
	get_transaction.customer_id AS transaction_customer_id,
	get_customer.id AS customer_customer_id,
	get_customer.name AS customer_name
FROM
	get_transaction
LEFT JOIN /* Experiment with inner, left, right, and full outer join */ 
	get_customer
ON
	get_transaction.customer_id = get_customer.id;
```

## `NULL` values in `NOT IN`

**This is perhaps the most dangerous pitfall, combining the principles we've learnt so far.** In the `NOT IN` filtering with subquery (`WHERE … NOT IN (SELECT …)`), if the subquery returns even a single `NULL` value, the main query will return 0 rows. A solution is to use [left antijoin](https://en.wikipedia.org/wiki/Join_(relational_algebra)#Antijoin) or [`NOT EXISTS`](https://www.postgresql.org/docs/16/functions-subquery.html).

❌ Bad example: This will return 0 rows.

```sql
WITH
	get_transaction AS (
		SELECT 1 AS id, 1000 AS transaction_amt, 1 AS customer_id
		UNION ALL SELECT 2, 2000, NULL
		UNION ALL SELECT 3, NULL, 3
		UNION ALL SELECT 4, 4000, 3
	),
	get_customer AS (
		SELECT 1 AS id, 'Alpha' AS name
		UNION ALL SELECT 2, NULL
		UNION ALL SELECT NULL, 'Gamma'
	)
SELECT
	get_transaction.id,
	get_transaction.transaction_amt,
	get_transaction.customer_id
FROM
	get_transaction
WHERE
	customer_id NOT IN (
		SELECT
			id
		FROM
			get_customer
	);
```

✅ Good example: Using left antijoin, this will return some rows.

```sql
WITH
	get_transaction AS (
		SELECT 1 AS id, 1000 AS transaction_amt, 1 AS customer_id
		UNION ALL SELECT 2, 2000, NULL
		UNION ALL SELECT 3, NULL, 3
		UNION ALL SELECT 4, 4000, 3
	),
	get_customer AS (
		SELECT 1 AS id, 'Alpha' AS name
		UNION ALL SELECT 2, NULL
		UNION ALL SELECT NULL, 'Gamma'
	)
SELECT
	get_transaction.id,
	get_transaction.transaction_amt,
	get_transaction.customer_id
FROM
	get_transaction
LEFT JOIN
	get_customer
ON
	get_transaction.customer_id = get_customer.id
WHERE
	get_customer.id IS NULL;
```

✅ Good example: Using `NOT EXISTS`, this will return some rows.

```sql
WITH
	get_transaction AS (
		SELECT 1 AS id, 1000 AS transaction_amt, 1 AS customer_id
		UNION ALL SELECT 2, 2000, NULL
		UNION ALL SELECT 3, NULL, 3
		UNION ALL SELECT 4, 4000, 3
	),
	get_customer AS (
		SELECT 1 AS id, 'Alpha' AS name
		UNION ALL SELECT 2, NULL
		UNION ALL SELECT NULL, 'Gamma'
	)
SELECT
    get_transaction.id,
    get_transaction.transaction_amt,
    get_transaction.customer_id
FROM
    get_transaction
WHERE
    NOT EXISTS (
        SELECT
	        1
        FROM
	        get_customer
        WHERE
	        get_transaction.customer_id = get_customer.id
    );
```
### Why does the main query return 0 rows if the subquery returns a single `NULL` value?

1. `customer_id NOT IN (1, 2, NULL)` translates to `customer_id <> 1 AND customer_id <> 2 AND customer_id <> NULL`.
2. `customer_id <> NULL` evaluates to `NULL`.
3. `… AND … AND NULL` evaluates to either `FALSE` or `NULL`, never `TRUE` for any values of `customer_id`.
4. The main query returns 0 rows.

### Why does it work with left antijoin?

1. In the regular left join (without `WHERE`), all rows in the `get_transaction` are included.
2. Join conditions:
	1. If the `customer_id` has the same value in both `get_transaction` and `get_customer`: The join will match, and the `customer_id` from `get_customer` will **not** be `NULL`.
	2. If the `customer_id` has `NULL` value in `get_transaction`, `get_customer`, or both: The join will **not** match, and the `customer_id` from `get_customer` will be `NULL`.
	3. If the `customer_id` has different value between `get_transaction` and `get_customer`: The join will **not** match, and the `customer_id` from `get_customer` will be `NULL`.
3. Adding `WHERE get_customer.id IS NULL` will exclude the data from join condition (2.i), while including data from join condition (2.ii) and (2.iii).
4. The main query returns data from join condition (2.ii) and (2.iii).

> [!NOTE]
> Even the PostgreSQL wiki [discourages](https://wiki.postgresql.org/wiki/Don't_Do_This#Don't_use_NOT_IN) using `NOT IN` and suggests `NOT EXISTS` instead.