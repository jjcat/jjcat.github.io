---
layout: index
title: JJCat Blog
---
{% assign first_post = site.posts.first %}
#{{ first_post.title }}#
{{ first_post.content }}
##latest posts##
<br>

test


<ul>
{% for post in site.posts %}
<li>{{ post.date | date_to_string }} <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>