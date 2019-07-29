---
layout: default
title: Blog Archive
---
<h2 class="title text-center">find / -type blog-post</h2>

<p><span style="color: red;">TODO:</span> All public posts, sorted by date. Draft version:</p>

<ul class="posts">
	{% for post in site.posts %}
	<li><span>[{{ post.date | date_to_string }}]</span>  <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>                                                                          
	{% endfor %}
</ul>
