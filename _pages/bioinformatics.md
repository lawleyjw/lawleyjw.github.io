---
title: "Bioinformatics"
permalink: /bioinformatics/
author_profile: true
---

<ul>
  {% for post in site.bioinformatics %}
    <li>
      <a href="{{ post.permalink }}">{{ post.title }}</a>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
