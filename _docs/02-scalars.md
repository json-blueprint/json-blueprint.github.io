---
title: "Matching Scalar Values"
permalink: /docs/scalar/
excerpt: "Validation of scalar JSON values."
last_modified_at: 2017-04-30T13:36:43+01:00
---

This section is a reference for patterns of scalar JSON values: boolean, string, number and null

{% include toc %}

## JSON Literals
Any valid JSON value can be used as a pattern to match itself. `"apple"` matches a string with value "apple", `4.2` matches number 4.2 and so on. Most often, this comes handy when combining literals with [choice](#choice) to create enumerations: `"apple" | "orange" | "banana"`

## Any
As the name suggest, `Any` matches any JSON value of any type, including object and array.

## Boolean
As you expect, `Boolean` matches either `true` or `false`.

## String
The `String` pattern matches any JSON string. You can specify additional constraints:

| name          | value type                 | description                              |
| ------------- | -------------------------- | ---------------------------------------- |
| **minLength** | Int(min = 0)               | minimal allowed length of string         |
| **maxLength** | Int(min = 0)               | maximal allowed length of string         |
| **pattern**   | String or JS regex literal | regular expression the string must match |

For example, the following pattern matches string of length 3 to 5: `String(minLength = 3, maxLength = 5)`

### Using Regular Expressions
Regular expressions can be specified through string or using JavaScript's regex literal syntax – `String(pattern = "[a-z]+")` is equivalent to `String(pattern = /[a-z]+/)`.

Only the regex literal syntax allows you to specify the "case insensitive" flag – `String(pattern = /[a-z]+/i)` matches both lower and upper case latin letters. Other JS regex flags can't be used.

If you don't want to specify any other contstraints, you can use the regex literal directly – `/[a-z]+/i` is equivalent to `String(pattern = /[a-z]+/i)`

Regular expressions must always match the value fully. They are implicitly anchored, so that `/[a-z]+/i` is matched the same way as `/^[a-z]+$/i`

## Number
The `Number` pattern matches any JSON number. You can specify additional constraints:

| name             | value type | a JSON number n satisfies contstraint value x if |
| ---------------- | ---------- | ------------------------------------------------ |
| **min**          | Number     | n >= x                                           |
| **minExclusive** | Number     | n > x                                            |
| **max**          | Number     | n <= x                                           |
| **maxExclusive** | Number     | n < x                                            |
| **multipleOf**   | Number     | n mod x == 0                                     |

For example, the following pattern matches number in the interval `<5.5, 12.7)`: `Number(min = 5.5, maxExclusive = 12.7)`

## Int
The `Int` pattern is same as `Number`, but only matches integers. You can also use the same kind of constraints as with `Number`:

| name             | value type | a JSON number n satisfies contstraint value x if |
| ---------------- | ---------- | ------------------------------------------------ |
| **min**          | Int        | n >= x                                           |
| **minExclusive** | Int        | n > x                                            |
| **max**          | Int        | n <= x                                           |
| **maxExclusive** | Int        | n < x                                            |
| **multipleOf**   | Int        | n mod x == 0                                     |

## Choice
<a name="choice"></a>
You can combine multiple patterns using `|`. JSON value is valid if it matches any one of the patterns combined in this way.

You can use choice to define enumeration: `"apple" | "orange" | "banana"`

But you can also combine different types of patterns: `"foo" | Int(min = 0) | Boolean`

You may also want to allow null value, for example if you accept integer or null: `Int | null`