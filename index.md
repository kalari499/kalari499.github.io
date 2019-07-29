---
layout: default
title: Welcome to kalari499's Blog
---
<h2 class="title text-center">ls latest_posts/</h2>

<p>
{% for post in site.posts limit:10 %}
Title: {{ post.title }}
<br/>
Description: {{ post.description }}
<br/>
Content: {{ post.excerpt | markdownify | strip_html | truncatewords: 50 }}
<br/>
{% endfor %}
</p>

<p>See all posts</p>
