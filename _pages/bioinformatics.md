---
title: "Bioinformatics"
permalink: /bioinformatics/
layout: archive
author_profile: true
---

{% include base_path %}

<ul>
  {% for post in site.bioinformatics %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
