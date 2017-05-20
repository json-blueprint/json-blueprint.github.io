---
title: "Comparison with JSON Schema"
permalink: /docs/vs-json-schema
excerpt: "Comparison of JSON Blueprint with JSON Schema."
last_modified_at: 2017-05-21T01:00:00+02:00
---

{% include toc %}

[JSON Schema](http://json-schema.org/) is a JSON-based format for describing the structure of JSON data. We'll discuss main differences between JSON Schema and JSON Blueprint.

## TLDR

| JSON Schema                         | JSON Blueprint                |
| ----------------------------------- | ----------------------------- |
| tooling-friendly (JSON format)      | human-friendly (external DSL) |
| extensive tooling support           | not much tooling (yet)        |
| mature                              | new and potentially unstable  |
| permissive by default               | strict by default             |

## Expressivity
JSON Schema is a JSON-based data format. It encodes information about the structure of JSON data in JSON itself. That has one obvious advantage – you can use existing JSON tools to work with it.

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

Also note that in the case of JSON Schema, the information whether given object property is required or optional is pretty far away from the definition of the property itself.

JSON Blueprint closely mirrors the structure of validated documents. Combined with the terse syntax, you can parse most of the information quickly. That is if you don't hate regular expressions with a passion, as we borrow a few syntactic constructs from them.

## Errors in Schema Definition
Let's consider the following simple JSON Schema:

```json
{
  "typo": "object",
  "properties": {
    "name": { "type": "string" },
    "age": { "type": "integer" }
  },
  "required": ["name", "age"]
}
```

Glancing over it, it seems that it only matches objects with `name` and `age`. If you read through the schema carefully, you may have noticed a typo (`typo` instead of `type`). Does it make the schema invalid? No, in fact, the schema is perfectly valid. However, it doesn't do what you expect. It would also match any non-object value (string, integer, any array...). 

That's because JSON Schema allows any non-keyword (`type`, `properties` and `required` are examples of keyword properties) to be used in the schema with any value. Additionally, a lot of keywords, including `properties` and `required`, only apply to a specific type of JSON value. Finally, empty JSON schema allows anything.

With JSON Schema, you're always one typo away from making your schema more permissive than you'd want to.
{: .notice--warning}

With JSON Blueprint, most schema errors should be caught by the parser. Although this depends on the quality of given parser, even if some error passes through, it will probably trigger validation error as JSON Blueprint is strict by default.

# Strictness
An empty JSON Schema (`{}`) matches any JSON value. In JSON Blueprint, the `{}` pattern would match an empty object and nothing else. This illustrates one fundamental difference between the two – by default, Schema is permissive and Blueprint is strict. You can still allow any value in Blueprint, but you have to be explicit about it: `Any`

There might be situations where strictness is not desirable and leads to unnecessary boilerplate. One example – in a schema for REST API payloads, you may want to always allow additional properties in any object to allow for forward compatibility. Cases like this should be covered by a global flag offered by the validator.