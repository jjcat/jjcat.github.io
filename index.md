---
layout: index
title: JJCat Blog
---
{% assign first_post = site.posts.first %}
# <a href = "{{ first_post.url }}"> {{ first_post.title }}</a>#
{{ first_post.date | date: "%B %e, %Y" }}
{{ first_post.content }}

<br>
<ul>
{% for post in site.posts %}
<li>{{ post.date | date_to_string }} <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>