---
title: "Quick-Start Guide"
permalink: /docs/quick-start-guide
excerpt: "Introduction to basic concepts of JSON Blueprint."
last_modified_at: 2017-05-15T21:30:00+02:00
---

JSON Blueprint is a simple domain specific language for validation of JSON. This guide introduces basic concepts to get you started.

{% include toc %}

## Patterns
With JSON Blueprint, you'll be constructing patterns to match JSON values. This is similar to regular expressions, where you construct patterns to match string values. Let's illustrate it with a simple example. The following is a simple JSON document that describes a Person:
```json
{
    "firstName": "John",
    "lastName": "Doe",
    "age": 42
}
```

In fact, this JSON document already is a valid pattern, albeit a very specific one. It would match a JSON object with `firstName` property equal to `"John"`, `lastName` equal to `"Doe"` and `age` equal to `42`. You can use any JSON literal as a pattern to match itself.

To be useful for validation, we have to generalize our pattern a little. To match any string, you can use the `String` pattern. `Int` matches any integer. Furthermore, some built-in data type patterns allow specifying additional constraints. As age cannot be negative, we can declare the lower-bound in the pattern: `Int(min = 0)`.
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="cm">/*
 * Note that simple property names don't have
 * to be quoted, much like in JavaScript
 */</span><span class="w">
</span><span class="p">{</span><span class="w">
    </span><span class="na">firstName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">lastName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">0</span><span class="p">)</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

This is a more useful pattern which would match any person, not just John Doe. Still, it very closely corresponds to our original JSON document.

## Schema
You don't use individual patterns as input for validation. Instead, you specify a collection of named patterns that we call a "schema". For example, let's specify a schema for people with addresses:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="c1">// definition of pattern named "Person"
</span><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">firstName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">lastName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">0</span><span class="p">),</span><span class="w">
    
    </span><span class="c1">// $Address is a reference to another named pattern
</span><span class="w">    </span><span class="na">address</span><span class="p">:</span><span class="w"> </span><span class="nv">$Address</span><span class="w"> 
</span><span class="p">}</span><span class="w">

</span><span class="cm"></span><span class="nx">Address</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">street</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">city</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">zip</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">countryCode</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

As you can see, you can refer to a named pattern from within another pattern by prefixing its name with `$`.

## Optional Properties
JSON Blueprint is strict by default. This means that the `Address` pattern above requires all 4 properties and allows no other.

Let's say we want to allow `Address`es without `zip` or `countryCode`. We can make a property optional by appending the `?` suffix to its definition:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Address</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">street</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">city</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="na">zip</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">)</span><span class="o">?</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="na">countryCode</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">)</span><span class="o">?</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

## Choice
If a value can be of multiple different types, separate multiple patterns with `|`. Validation passes if any one of these pattern matches:

<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">FlexibleEntry</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">id</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="nb">String</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="kc">null</span><span class="p">,</span><span class="w">
    </span><span class="na">name</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="kc">null</span><span class="w"> 
</span><span class="p">}</span></code>
</pre>
</div>

The above pattern would match f.ex.:
```json
{
    "id": 1,
    "name": null
}
```

or 

```json
{
    "id": "86f7e437faa5a7fce15d1ddcb9eaeaea377667b8",
    "name": "Foo"
}
```

## Recursive Patterns
Any pattern in a schema can reference itself to create a recursive definition: 

<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">IntTree</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="nb">Int</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">left</span><span class="p">:</span><span class="w"> </span><span class="nv">$IntTree</span><span class="p">,</span><span class="w">
    </span><span class="na">right</span><span class="p">:</span><span class="w"> </span><span class="nv">$IntTree</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

The `IntTree` pattern above would match for example:

```json
{
    "left": {
        "left": 1,
        "right": 2
    },
    "right": 3
}
```

## Next Steps
Learn more about patterns for [scalar values](/docs/scalar), [arrays](/docs/array) or [objects](/docs/object). You can also check some sample schemas and try creating your own using our [online validator](/validator).