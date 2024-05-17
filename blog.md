---
layout: default
---

# Blog

[<i class="fa-solid fa-rss"></i> RSS feed](/feed.xml)

{%- for post in site.posts %}

* [{{post.date | date: "%Y-%m-%d"}} {{post.title}}]({{ post.url }})
{%- endfor %}
