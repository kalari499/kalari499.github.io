---
layout: default
title: My Playground
---
<h2 class="title text-center">ls latest-posts/</h2>

<div id="front-page">
{% for post in site.posts limit:15 %}
<div class="post">
<a class="title" href="{{ post.url }}">{{ post.title }}</a>
<p class="meta">kalari499 / {{ post.category }} / {{ post.date | date_to_long_string }}</p>
<p class="preview">{{ post.description }}</p>
</div>
{% endfor %}
</div>

<div class="text-center">
<a class="see-all" href="/archive">See all posts</a>
</div>
