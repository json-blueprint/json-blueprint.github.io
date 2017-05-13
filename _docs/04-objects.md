---
title: "Matching Objects"
permalink: /docs/object/
excerpt: "Validation of JSON objects."
last_modified_at: 2017-04-30T13:36:43+01:00
---

In this section, we'll look at patterns for JSON objects.

{% include toc %}

## Basic Object Patterns
A basic object pattern in JSON Blueprint looks like JavaScript object literal in which property values are patterns. Property names don't have to be qouted if they are valid JS identifiers (with one exception – property names containing `$` have to be quoted):

```json-blueprint
Person = {
    firstName: String,
    lastName: String,
    age: Int,
    "$sInThePocket": Number
}
```

JSON Blueprint is strict by default. That means that the pattern above would not allow any additional properties besides the 4 listed in the pattern.

## Optional Properties
We can indicate optional property in the same way we'd indicate optional array item – by suffixing it with `?`. However, as both property name and value would be missing, we have to enclose them in parentheses:

```json-blueprint
Person = {
    name: String, // mandatory
    (age: Int)?   // optional
}
```

## Wildcard Properties
In previous examples, all of the property names were specified exactly as they should appear in the JSON document. However, we can use any pattern that matches strings in the property name position:

```json-blueprint
Person = {
    name: String
    (/_int.*/: Int)*,
    (String: Any)*,
    (age: Int)?
}
```

The above pattern would allow any number of additional properties with any value, but if the property name starts with `_int`, the value must be an integer. You can use the same suffixes as with array items to indicate number of allowed repetitions of wildcard properties.

When using wildcard property patterns, there will usually be some overlap of property names. There are two rules of precendence:
1. A literal property name pattern always has precedence over any wildcard one, regardless of position
2. A wildcard property name pattern that comes before another wildcard pattern has precedence

Going back to our example, if we switch positions of `(/_int.*/: Int)*` and `(String: Any)*`, even properties starting with `_int` would be allowed to have any value as the `/_int.*/` pattern would be shadowed. On the other hand, the `age` property is literal and so it isn't affected by it's position in the object pattern.

## Property Group
You can indicate some properties must either be all present or all missing grouping them in parentheses like so:

```json-blueprint
Person = {
    name: String,
    (
        street: String,
        city: String,
        zip: String
    )?
}
```

In this case, all of the address properties must be present or they have to all be missing. If each individual property had been made optional, a document with just one or two of them would be valid.

## Property Choice
You can use the choice operator `|` in object pattern to indicate mutually exclusive properties. You can even specify choice between mutually exclusive groups of properties. The following is a pattern of a point in 2D space with either cartesian or polar coordinates (but not both):

```json-blueprint
Point = {
    label: String,
    (
        x: Number,
        y: Number
    ) | (
        r: Number,
        phi: Number
    )
}
```

## Mixins
You can extract common properties into a separate object pattern and mix it into definition of other object patterns. Consider the following example of a REST API schema, where the common shape of paged response is described by the `Page` pattern. This is then mixed into `VideoList`:

```json-blueprint
VideoList = $Page with {
    // overrides definition of items from $Page
    items: [$Video*]
}

Page =  {
    // items don't have to be here
    // in this case it's just a form of documentation
    items: [Any*],
    links: {
        self: String,
        (previous: String)?,
        (next: String)?
    }
}

Video = {
    id: String,
    dimension: "2d" | "3d",
    definition: "hd" | "sd"
}
```

A few notes:
* each object pattern can have multiple mixins (the syntax is `$A with $B with $C ...`)
* patterns that come later in the declaration may override definition of properties that come before them
* literal property names always have precedence over wildcards – even if `VideoList` in the example contained `(String: Any)*`, it wouldn't override definition of `links` in `Page`