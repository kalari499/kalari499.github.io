---
layout: default
title: Welcome to kalari499's Blog
---
<h2 class="title text-center">ls latest_posts/</h2>

<div id="front-page">
{% for post in site.posts reversed limit:15 %}
<div class="post">
<a class="title" href="{{ post.url }}">{{ post.title }}</a>
<p class="meta">kalari499 / {{ post.category }} / {{ post.date | date_to_string }}</p>
<p class="preview">{{ post.description }}</p>
</div>
{% endfor %}
</div>

<div class="text-center">
<a class="see-all" href="/archive">See all posts</a>
</div>
