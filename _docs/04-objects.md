---
title: "Matching Objects"
permalink: /docs/object/
excerpt: "Validation of JSON objects."
last_modified_at: 2017-05-15T23:30:00+02:00
---

In this section, we'll take a look at patterns for JSON objects.

{% include toc %}

## Basic Object Patterns
A basic object pattern in JSON Blueprint looks like JavaScript object literal in which property values are patterns. Property names don't have to be quoted if they are valid JS identifiers (with one exception – property names containing `$` have to be quoted):
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">firstName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">lastName</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">,</span><span class="w">
    </span><span class="s2">"$sInThePocket"</span><span class="p">:</span><span class="w"> </span><span class="nb">Number</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

JSON Blueprint is strict by default. That means that the pattern above would not allow any additional properties besides the 4 listed in the pattern.

## Optional Properties
We can indicate optional property in the same way we'd indicate optional array item – by suffixing it with `?`. However, as both property name and value would be missing, we have to enclose them in parentheses:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">name</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w"> </span><span class="c1">// mandatory
</span><span class="w">    </span><span class="p">(</span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">)</span><span class="o">?</span><span class="w">   </span><span class="c1">// optional
</span><span class="p">}</span></code>
</pre>
</div>

## Wildcard Properties
In previous examples, all of the property names were specified exactly as they should appear in the JSON document. However, we can use any pattern for strings in the property name position:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">name</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="w">
    </span><span class="p">(</span><span class="sr">/_int.*/</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">)</span><span class="o">*</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="na">String</span><span class="p">:</span><span class="w"> </span><span class="nx">Any</span><span class="p">)</span><span class="o">*</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="na">age</span><span class="p">:</span><span class="w"> </span><span class="nb">Int</span><span class="p">)</span><span class="o">?</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

Above pattern allows any number of additional properties with any value, but if the property name starts with `_int`, the value must be an integer. You can use the same suffixes as with array items to indicate number of allowed repetitions of wildcard properties.

When using wildcard property patterns, there will usually be some overlap of property names. There are two rules of precedence for property name patterns:
1. A literal pattern always has precedence over any wildcard one, regardless of position
2. A wildcard pattern that comes before another wildcard pattern has precedence

Going back to our example, if we switch positions of `(/_int.*/: Int)*` and `(String: Any)*`, even properties starting with `_int` would be allowed to have any value as the `/_int.*/` pattern would be shadowed by `(String: Any)*`. On the other hand, the `age` property is literal and so it isn't affected by its position in the object pattern.

## Property Group
You can indicate some properties must either be all present or all missing by grouping them in parentheses like so:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Person</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">name</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="w">
        </span><span class="na">street</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
        </span><span class="na">city</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
        </span><span class="na">zip</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="w">
    </span><span class="p">)</span><span class="o">?</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

In this case, all of the address properties must be present or they have to all be missing. If each individual property had been made optional, a document with just one or two of them would be valid.

## Property Choice
You can use the choice operator `|` in object pattern to indicate mutually exclusive properties. You can even specify a choice between mutually exclusive groups of properties. The following is a pattern of a point in 2D space with either cartesian or polar coordinates (but not both):
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">Point</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">label</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="p">(</span><span class="w">
        </span><span class="na">x</span><span class="p">:</span><span class="w"> </span><span class="nb">Number</span><span class="p">,</span><span class="w">
        </span><span class="na">y</span><span class="p">:</span><span class="w"> </span><span class="nb">Number</span><span class="w">
    </span><span class="p">)</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="p">(</span><span class="w">
        </span><span class="na">r</span><span class="p">:</span><span class="w"> </span><span class="nb">Number</span><span class="p">,</span><span class="w">
        </span><span class="na">phi</span><span class="p">:</span><span class="w"> </span><span class="nb">Number</span><span class="w">
    </span><span class="p">)</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

## Mixins
You can extract common properties into a separate object pattern and mix it into the definition of other object patterns. Consider the following example of a REST API schema, where the common shape of paged response is described by the `Page` pattern. This is then mixed into `VideoList`:
<div class="highlighter-rouge language-json-blueprint">
<pre class="highlight">
<code><span class="nx">VideoList</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="nv">$Page</span><span class="w"> </span><span class="kr">with</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="c1">// overrides definition of items from $Page
</span><span class="w">    </span><span class="na">items</span><span class="p">:</span><span class="w"> </span><span class="p">[</span><span class="nv">$Video</span><span class="o">*</span><span class="p">]</span><span class="w">
</span><span class="p">}</span><span class="w">

</span><span class="nx">Page</span><span class="w"> </span><span class="o">=</span><span class="w">  </span><span class="p">{</span><span class="w">
    </span><span class="c1">// this property don't have to be here
</span><span class="w">    </span><span class="c1">// in this case it's just a form of documentation
</span><span class="w">    </span><span class="na">items</span><span class="p">:</span><span class="w"> </span><span class="p">[</span><span class="nx">Any</span><span class="o">*</span><span class="p">],</span><span class="w">
    </span><span class="na">links</span><span class="p">:</span><span class="w"> </span><span class="p">{</span><span class="w">
        </span><span class="na">self</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
        </span><span class="p">(</span><span class="na">previous</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">)</span><span class="o">?</span><span class="p">,</span><span class="w">
        </span><span class="p">(</span><span class="na">next</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">)</span><span class="o">?</span><span class="w">
    </span><span class="p">}</span><span class="w">
</span><span class="p">}</span><span class="w">

</span><span class="nx">Video</span><span class="w"> </span><span class="o">=</span><span class="w"> </span><span class="p">{</span><span class="w">
    </span><span class="na">id</span><span class="p">:</span><span class="w"> </span><span class="nb">String</span><span class="p">,</span><span class="w">
    </span><span class="na">dimension</span><span class="p">:</span><span class="w"> </span><span class="s2">"2d"</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="s2">"3d"</span><span class="p">,</span><span class="w">
    </span><span class="na">definition</span><span class="p">:</span><span class="w"> </span><span class="s2">"hd"</span><span class="w"> </span><span class="o">|</span><span class="w"> </span><span class="s2">"sd"</span><span class="w">
</span><span class="p">}</span></code>
</pre>
</div>

A few notes:
* each object pattern can have multiple mixins (the syntax is `$A with $B with $C ...`)
* patterns that come later in the declaration may override properties of patterns that come before them
* literal property names always have precedence over wildcards – even if `VideoList` in the example contained `(String: Any)*`, it wouldn't override the definition of `links` in `Page`