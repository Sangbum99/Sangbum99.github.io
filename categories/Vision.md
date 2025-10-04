---
layout: page
title: Vision
permalink: /blog/categories/Vision/
---

<h5> Posts by Category : {{ page.title }} </h5>

<div class="card">
{% for post in site.categories.Vision %}
 <li class="category-posts"><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.link }}" target="_blank" rel="noopener noreferrer">{{ post.title }}</a></li>
{% endfor %}
</div>