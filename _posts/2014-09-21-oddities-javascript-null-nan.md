---
layout: post
title: Oddities in JavaScript's 'NaN' and 'null'
---

# Oddities in JavaScript's 'NaN' and 'null'

<p class="meta">21 September 2014 - San Francisco</p>

JavaScript is a beautiful but strange beast. It is the lingua franca of the web and one of the most widely used programming languages ever, yet it was only designed in 10 days. One of its great strengths is in its extreme (and according to some critics excessive) flexibility, but has also led to some unexpected and bizarre consequences.

Please enjoy the below gems for 'NaN' and 'null':

{% highlight javascript %}
// NaN
console.log(typeof NaN);
=> number
console.log(NaN === NaN);
=> false
console.log(Math.max(NaN, 10));
=> NaN
console.log(Math.min(NaN, 10));
=> NaN


// null
console.log(typeof null);
=> object
console.log(null instanceof Object);
=> false
console.log(null === null);
=> true
console.log(Math.max(null, 10));
=> 10
console.log(Math.min(null, 10));
=> 0
{% endhighlight %}
<br>