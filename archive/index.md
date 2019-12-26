---
layout: default
title: Blog Archive
---
<h2 class="title text-center">find / -type blog-post</h2>

{% for category in site.categories %}
  <p id="{{ category | first }}">{{ category | first }}</p>
  <ul class="posts">
    {% for post in category.last reversed %}
      <li><span>[{{ post.date | date: "%d/%m/%y" }}]</span> <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
