---
title: "Matching Arrays"
permalink: /docs/array
excerpt: "Validation of JSON arrays."
last_modified_at: 2017-05-15T21:50:00+02:00
---

In this section, we'll take a look at patterns for JSON arrays.

{% include toc %}

## Fixed-length Arrays
You can simply provide an array of [scalar patterns](/docs/scalar) to match elements positionally. For example, to match a single string followed by a boolean:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="p">[</span><span class="nb">String</span><span class="p">,</span><span class="w"> </span><span class="nb">Boolean</span><span class="p">]</span></code>
</pre>
</div>

The above would match `["foo", true]` or `["bar", false]`, but not `[true, "foo"]` (the element order is flipped) or `["foo", true, false]` (too many elements).

## Repeated Array Elements
Most of the time, you'll be probably dealing with arrays in which all elements share the same type. You can append a suffix to any pattern to indicate allowed number of repetitions in the array:

| pattern           | array with ...               |
| ----------------- | ---------------------------- |
| `[String?]`       | one string or an empty array |
| `[String*]`       | zero or more strings         |
| `[String+]`       | one or more strings          |
| `[String{5}]`     | exactly 5 strings            |
| `[String{5,}]`    | 5 or more strings            |
| `[String{5, 10}]` | 5 to 10 strings              |

Repeated patterns can be freely combined with other patterns:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="p">[</span><span class="nb">Int</span><span class="p">,</span><span class="w"> </span><span class="nb">String</span><span class="o">*</span><span class="p">,</span><span class="w"> </span><span class="nb">Boolean</span><span class="p">]</span></code>
</pre>
</div>

The above matches for example `[1, true]` or `[1, "foo", true]` or `[1, "foo", "bar", true]`

## Repeated Groups
You can enclose multiple patterns in parentheses with repetition suffix to create a repeated group:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="p">[(</span><span class="nb">Int</span><span class="p">,</span><span class="w"> </span><span class="nb">Boolean</span><span class="p">)</span><span class="o">+</span><span class="p">]</span></code>
</pre>
</div>

This pattern would match one or more pairs of integer and boolean. Both `[1, true]` and `[1, true, 2, false]` are examples of valid values. `[1, true, 2]` is not a valid value as it's missing a boolean at the end.

## Go Crazy
Combining all of what we've learned so far, you can create some truly fear-inducing patterns:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="p">[</span><span class="nb">Int</span><span class="p">(</span><span class="nx">min</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="mi">1</span><span class="p">),</span><span class="w"> </span><span class="p">(</span><span class="sr">/[a-z][a-z0-9_]*/i</span><span class="p">)</span><span class="o">*</span><span class="p">,</span><span class="w"> </span><span class="p">(</span><span class="nb">Int</span><span class="p">,</span><span class="w"> </span><span class="nb">Boolean</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="nb">String</span><span class="p">)</span><span class="o">?</span><span class="p">]</span></code>
</pre>
</div>

This rather complicated creation would match an array starting with a natural number, followed by zero or many identifiers, followed optionally by an integer. And only if there was an integer, followed by either boolean or string.

You can still kind of read it, let's hope you don't ever need something like this.