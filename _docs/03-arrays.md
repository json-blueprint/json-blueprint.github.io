---
title: "Matching Arrays"
permalink: /docs/array/
excerpt: "Validation of JSON arrays."
last_modified_at: 2017-04-30T13:36:43+01:00
---

This section is a reference for patterns of scalar JSON values: boolean, string, number and null

{% include toc %}

## Fixed-length Arrays
You can simply enclose [scalar patterns](/docs/scalar) in an array to match elements positionally. For example, to match a single string followed by a boolean:
```json-blueprint
[String, Boolean]
``` 
The above would match `["foo", true]` or `["bar", false]`, but not `[true, "foo"]` (the element order is flipped) or `["foo", true, false]` (too many elements).

## Repeated Array Elements
Most of the time, you'll be probably dealing with arrays of uniform elements. You can append a suffix to any pattern to indicate number of repetitions:

| pattern           | array with ...               |
| ----------------- | ---------------------------- |
| `[String?]`       | one string or an empty array |
| `[String*]`       | zero or more strings         |
| `[String+]`       | one or more strings          |
| `[String{5}]`     | exactly 5 strings            |
| `[String{5,}]`    | 5 or more strings            |
| `[String{5, 10}]` | 5 to 10 strings              |

Repeated patterns can be freely combined with other patterns:
```json-blueprint
[Int, String*, Boolean]
```
The above matches for example `[1, true]` or `[1, "foo", true]` or `[1, "foo", "bar", true]`

## Repeated Groups
You can enclose multiple patterns in parens with repetition suffix to create a repeated group:
```json-blueprint
[(Int, Boolean)+]
```
This pattern would match one or more pairs of integer and boolean. Both `[1, true]` and `[1, true, 2, false]` are examples of valid values. `[1, true, 2]` is not a valid value as it's missing a boolean at the end.

## Go Crazy
Combining all of what we've learned so far, you can create some trully fear-inducing patterns:
```json-blueprint
[Int(min = 1), (/[a-z][a-z0-9_]*/i)*, (Int, Boolean | String)?]
```
This otherworldly creation would match an array starting with a natural number, followed by zero or many identifiers, followed optionally by an integer and either boolean or string.

You can still kind of read it, but should you ever need something like this? You know the answer...