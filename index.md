---
layout: page
title: tech, thought, and communication
---
{% include JB/setup %}

## Recent Posts：

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



Thanks for the theme supported by [Jekyll-bootstrap](http://jekyllbootstrap.com/) .