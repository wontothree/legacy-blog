---
title: "Project"
layout: archive
permalink: /project
sidebar:
    nav: "sidebar-category"
---

{% assign posts = site.categories.project %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
