---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

<ul class="post-list">
{% for post in site.posts %}
  <li> <span class="post-meta">{{post.date}}</span>
    <h3>
          <a class="post-link" href="{{ site.baseurl }}{{ post.url }}">
            {{ post.title }}
          </a>
        </h3>
{% endfor %}
