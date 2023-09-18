---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

# Research

## Journals

{% bibliography --query @article %}

## Proceedings

These are all peer-reviewed, long papers.

{% bibliography --query @*[booktitle ^= Proceedings] %}

## Theses

{% bibliography --query @*[tag = thesis] %}
