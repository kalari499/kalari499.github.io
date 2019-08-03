---
layout: default
---
<h2 class="title text-center">{{ page.title }}</h2>
<h4 class="meta text-center">Posted on {{ page.date | date_to_long_string }}</h4>

<div class="post">
    {{ content }}
</div>
