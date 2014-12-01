---
layout: post
title: JavaScript Labels Explained
---

# JavaScript Labels: Explained

<p class="meta">25 November 2014 - San Francisco</p>

I recently went to a talk by the creator of [human.js](https://github.com/Willyham/human.js) about writing readable code, a central tenet of which is avoiding JavaScript "quirks". The purpose of this post is to enlighten you about one of these quirks--labels--and equip you with the knowledge of their meaning to the interpreter and why you should avoid them. Even though you should never use them yourself, you may not be able to avoid them in certain situations when dealing with other people's code.

The impetus for this blog post is an open source library I used recently, [jquery-handsontable](https://github.com/handsontable/jquery-handsontable), which essentially provides an Excel-like spreadsheet UI out of the box using purely jQuery. I was reading through the library and suddenly came across this statement in one of the files:

<pre><code>_handle_error:
</code></pre>

My initial reaction, given the underscore and colon syntax, was that this was a "private" property of an object. Yet it was clear from the surrounding code that this line was not inside an object literal declaration. I ran a global search on the repo for '_handle_error' and came across no additional results...weird. So even though no other part of the code explicitly referred to this line, I discovered we had a JavaScript label on our hands.

For those of you with C or BASIC experience this may be intuitive. I wrote a more-than-healthy amount of TI-BASIC while bored in middle school Math class and would often write statements like this:

<pre><code>: Lbl A
: ...
: If (some condition)
: Goto A</code></pre>


Essentially what is happening here is that you run some code, and if some conditional is met "jump" to a different place in your code--the simplest analogy for this may be a "Choose Your Own Adventure" book. If you choose to face and fight the goblin, turn to page 47; if you choose to escape into the tunnel and run away, turn to page 39. You can use them to repeat a bit of code, refer to a specific loop (if you have several nested loops), or jump ahead.

Semantically the idea for `_handle_error:` is to jump to a place in your code that will handle an error. But the question that any programmer should ask is, why not call a function or throw an error instead? Both of these approaches have their advantages--calling a function will define a new scope where you can sandbox and namespace data, and throwing an error will help identify what is wrong with the inputs or code in the first place to aid the user and/or developer. A function would have expected input types and a return type, whereas it is difficult to predict the <em>state</em> of an application after jumping to a label.

So that's it--know what labels are but try to avoid using them.

## More Resources

[MDN on JavaScript Labels](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)
