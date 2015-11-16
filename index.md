---
title: Home
layout: page
---

Welcome to my blog!

Usually here I'll post tutorials for packages I've developed
or languages I'm fond of, with the odd opinion mixed in.

## Recent posts: ##

{% for post in site.posts limit:4 %}  

[{{ post.name }}]({{ post.url }})

{% endfor %}

[Full archives.](/archive)
