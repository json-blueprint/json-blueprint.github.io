---
title: "Quick-Start Guide"
permalink: /docs/quick-start-guide/
excerpt: "Introduction to basic concepts of JSON Schema."
last_modified_at: 2017-04-30T13:36:43+01:00
---

JSON Blueprint is a simple domain specific language for validation of JSON. This guide introduces basic concepts to get you started.

{% include toc %}

## Patterns
JSON Blueprint is all about patterns for JSON values. Let's create a pattern for the following JSON document describing a person:
```json
{
    "firstName": "John",
    "lastName": "Doe",
    "age": 42
}
```

In fact, this JSON document already is a valid pattern, albeit a very specific one. It would match any JSON object with `firstName` property equal to `"John"`, `lastName` equal to `"Doe"` and `age` equal to `42`. JSON Blueprint is a superset of JSON, so you can use any regular JSON value to match itself.

Let's abstract the pattern to match somebody else than John Doe as well. Pattern to match any string is `String` and for integers, it's `Int`. Furthermore, some built-in data type patterns allow specifying additional constraints. As age cannot be negative, we can declare the lower-bound in the pattern: `Int(min = 0)`. One last thing – you can leave out quotes for simple property names. This gives us the following pattern:
<div class="highlighter-rouge language-json">
<pre class="highlight">
<code><span class="p">{</span><span class="w">
    </span><span class="na">firstName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">lastName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">0</span><span class="p">)</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

As you can see, the pattern closely resembles matching JSON documents.

## Schema
You don't use individual patterns as input for validation. Instead, you specify a collection of named patterns that we call a "schema". For example, let's specify a schema for our "people documents":
<div class="highlighter-rouge language-json">
<pre class="highlight">
<code><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">firstName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">lastName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">0</span><span class="p">),</span><span class="w">
    </span><span class="na">address</span><span class="p">:</span><span class="w"> </span><span class="nv">$Address</span><span class="w">
</span><span class="p">}</span><span class="w">

</span><span class="nx">Address</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">street</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">city</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">zip</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">countryCode</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

As you can see, you can refer to a named pattern from within another pattern by prefixing it's name with `$`.

## Optional Properties
JSON Blueprint is strict by default. This means that the `Address` pattern above requires all 4 properties and allows no other.

Let's say we want to allow `Address`es without `zip` or `countryCode`. We can make a property optional by appending the `?` suffix to it:
```json-blueprint
Address = {
    street: String,
    city: String,
    (zip: String)?,
    (countryCode: String)?
}
```

## Choice
TODO

## Recursive Patterns
Any pattern in a schema can reference itself to create a recursive definition: 

```json-blueprint
IntTree = {
    left: $IntTree,
    right: $IntTree
} | Int
```

The `IntTree` pattern would match for example:

```json
{
    "left": {
        "left": 1,
        "right": 2
    },
    "right": 3
}
```