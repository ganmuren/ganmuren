---
layout: page
title: Contents
tagline: of Software Development Inspirations...
---
{% include JB/setup %}

Hello there, I build this site for persisting the inspiration of my programmer life... Here's the contents:
<br/>
<img src="/images/contents.png" width="32" height="32" />
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }} -> {{post.tagline }}</a></li>
  {% endfor %}
</ul>




