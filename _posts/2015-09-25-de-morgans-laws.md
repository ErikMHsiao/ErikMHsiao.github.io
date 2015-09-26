---
layout: post
title: De Morgan's Laws
---

<b>De Morgan's laws</b> are a pair of boolean algebra rules, if written in javascript-style boolean expressions, they look like this:

```
!(A && B) is equivalent to !A || !B
!(A || B) is equivalent to !A && !B
```
<br>
This is not only useful for boolean simplification, but also for general problem solving. For example, in underscore.js there is a function called <b>_.every</b> that returns true if every value in a collection passes a truth test. There is also a function called <b>_.some</b> that returns true if any value in the collection passes the truth test. Normally, the logic of <b>_.some</b> can be written similar to this:

```javascript
var _.some = function(collection, test) {
  var anyPass = false;

  for (var i = 0; i < collection.length; i++) {
    anyPass = anyPass || (test(collection[i]));
  }

  return anyPass;
};
```

This straightforward implementation can be simplified slightly with <b>_.reduce</b>. However, there is also a clever way to reimplement the same function with <b>_.every</b>. Can you think of a way? <i>(Hint: It has to do with the title of this blog post.)</i>
<br>
<br>
<br>
The trick depends on the overlap between the two functions. The relationship may not be apparent at first, but using <b>De Morgan's laws</b>, it can be shown rather easily:

```
    A || B || C
->  !!(A || B || C)
->  !(!A && !B && !C)
```

Using a double negation, we yield a logical equivalency between two boolean expressions that can be summarized as this:

<b>Some elements pass the truth test</b> is equivalent to <b>Not all elements fail the truth test</b>.
<br>
<br>
With this knowledge in hand, we can reimplement <b>_.some</b> with some cleverness, as shown here:

```javascript
var _.some = function(collection, test) {
  return !_.every(collection, function(element) {
    return !test(element);
  });
};
```