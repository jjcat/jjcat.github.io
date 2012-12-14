---
layout: index
title: JJCat Blog
---
{% for post in site.posts limit:10 %}
# <a href = "{{ post.url }}"> {{ post.title }}</a>#
{{ post.date | date: "%B %e, %Y" }}
{{ post.content | truncatewords: 10}}
<br>
<a href = "{{post.url}}">More</a>
<br>

-------------------
<br>

{% endfor %}

<br>

<a href = "/archive.html">All Posts</a>