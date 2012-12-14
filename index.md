---
layout: index
title: JJCat Blog
---
{% for post in site.posts limit:10 %}
# <a href = "{{ post.url }}"> {{ post.title }}</a>#
{{ post.date | date: "%B %e, %Y" }}
{{ post.content }}

-------------------
<br>

{% endfor %}

<br>
<ul>
{% for post in site.posts %}
<li>{{ post.date | date_to_string }} <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>