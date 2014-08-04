---
layout: post
title: Syntax Highlighting
excerpt: "I love syntax highlighting!"
tags: [sample post, code, highlighting]
comments: true
---

[Syntax highlighting](http://en.wikipedia.org/wiki/Syntax_highlighting) is a feature that displays source code, in different colors and fonts according to the category of terms. This feature facilitates writing in a structured language such as a programming language or a markup language as both structures and syntax errors are visually distinct. Highlighting does not affect the meaning of the text itself; it is intended only for human readers.

### Check it out

Let's try out a quick little ruby snippet... Memoized recursive factorials anyone?(!)

{% highlight ruby %}
@stat = [] def fact n n == 0 ? 1 : @stat[n] ||= n * fact(n-1) end
{% endhighlight %}

And that's the gist of it...

{% gist broinjc/db6e0ac214c355c887e5 %}

> Though poetry can woo you, the coding high is what keeps you hooked