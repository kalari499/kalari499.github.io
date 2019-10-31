---
layout: default
title: Blog Archive
---
<h2 class="title text-center">find / -type blog-post</h2>

<ul class="posts">
	{% for post in site.posts reversed %}
	<li><span>[{{ post.date | date: "%d/%m/%Y" }}]</span>  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>
