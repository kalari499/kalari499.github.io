---
layout: default
title: Blog Archive
---
<h2 class="title text-center">find / -type blog-post</h2>

<p><span style="color: red;">TODO:</span> All public posts, from oldest. Front-page only 15 latest posts. Draft version:</p>

<ul class="posts">
	{% for post in site.posts reversed %}
	<li><span>[{{ post.date | date: "%d / %m / %Y" }}]</span>  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
	{% endfor %}
</ul>
