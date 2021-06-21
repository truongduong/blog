---
layout: page
title: Category
permalink: /category/
---

{% for category in site.categories %}
  {% capture category_name %}{{ category | first }}{% endcapture %}
  <h2 class="post-list-heading">{{ category_name }} ({{ category[1].size }})</h2>
  {% for post in site.categories[category_name] %}
  {{ post.date | date: "%D" }} <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
  {% endfor %}
{% endfor %}