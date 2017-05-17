---
title: "Comparison with JSON Schema"
permalink: /docs/vs-json-schema/
excerpt: "Comparison of JSON Blueprint with JSON Schema."
last_modified_at: 2017-04-30T13:36:43+01:00
---

[JSON Schema](http://json-schema.org/) is a JSON-based format for describing the structure of JSON data. I'll discuss main differences between JSON Schema and JSON Blueprint.

{% include toc %}

## Expressivity
JSON Schema is a JSON-based data format. It encodes information about structure of JSON data in JSON itself. That has one obvious advantage – you can use existing JSON tools to work with it.

On the other hand, this representation is not very expressive. You can see the difference even on some very simple examples. Let's see how you'd match a JSON string `"foo"` in both languages:

<div class="code-comparison">
<div class="code-sample" markdown="1">
<p class="language">JSON Schema</p>
```json
{
  "const": "foo"
}
``` 
</div>

<div class="code-sample" markdown="1">
<p class="language">JSON Blueprint</p>
<div class="highlighter-rouge language-json">
<pre class="highlight">
<code><span class="s2">"foo"</span></code>
</pre>
</div>
</div>
</div>

In this simple case, the "syntactic overhead" of JSON Schema is relatively minor. But it quickly adds up with more complex JSON structure:

<div class="code-comparison">
<div class="code-sample" markdown="1">
<p class="language">JSON Schema</p>
```json
{
  "type": "object",
  "properties": {
    "id": { 
      "type": "integer",
      "min": 0
    },
    "name": { "type": "string" },
    "tags": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "required": ["id", "name"]
}
``` 
</div>

<div class="code-sample" markdown="1">
<p class="language">JSON Blueprint</p>
<div class="highlighter-rouge language-json">
<pre class="highlight">
<code><span class="p">{</span><span class="w">
  </span><span class="na">id</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">0</span><span class="p">),</span><span class="w">
  </span><span class="na">name</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
  </span><span class="p">(</span><span class="na">tags</span><span class="p">:</span><span class="w"> </span><span class="p">[</span><span class="nb">String</span><span class="o">*</span><span class="p">])</span><span class="o">?</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>
</div>
</div>

Also note that in case of JSON Schema, the information whether given object property is required or optional is pretty far away from the definition of property itself.

JSON Blueprint closely mirrors the structure of validated documents. Combined with the terse syntax, you can parse most of the information quickly. That is if you don't hate regular expressions with a passion, as we borrow a few syntactic constructs from them.

## Errors in Schema Definition
Let's consider the following simple JSON Schema:

```json
{
  "type": "object",
  "items": {
    "name": { "type": "string" },
    "age": { "type": "string" }
  }
}
```

Although it's perfectly valid, it probably doesn't do what you'd expect on first look. It says we require value of type `object`, but uses the `items` keyword which only works with arrays. You can still use this schema, but it would allow any object. 

To fix this, we may change the `items` keyword to `properties`. Such Schema would match an object with optional `name` and `age`. But let's say we wanted to validate an array in the first place:

```json
{
  "type": "array",
  "items": {
    "name": { "type": "string" },
    "age": { "type": "string" }
  }
}
```

This is again a valid schema. Value of `items` is a schema for each array item, so might look like it would match an array of objects with `name` and `age`. But in reality, any array would pass. That's because to validate object properties, one would have to wrap them in the `properties` keyword. The problem is that any non-keyword property is allowed to appear in the Schema with any value.

With JSON Schema, you're always one typo away from making your schema more relaxed than you'd want to.
{: .notice--warning}

With JSON Blueprint, most schema errors should be caught by the parser. Although this depends on quality of given parser, even if some error passes through, it will probably trigger validation error as JSON Blueprint is strict by default.

# Strictness