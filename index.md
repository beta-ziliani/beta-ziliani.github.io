---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<ul>
   {% for item in site.data.navlist %}
      <li><a href="{{ item.url }}">{{ item.title }}</a></li>
   {% endfor %}
</ul>

# About

This page is at the moment entirely devoted to my professional life, as a developer, as a researcher, and as a professor.

> **Note:** Last updated: {{ "now" | date: "%Y-%m-%d %H:%M" }}.
