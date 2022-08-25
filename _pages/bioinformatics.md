---
title: "Bioinformatics"
permalink: /bioinformatics/
author_profile: true
---

{% for workflow in site.bioinformatics %}
  <h2>
    <a href="{{ workflow.url }}">
      {{ workflow.title }}
    </a>
  </h2>
  <p>{{ workflow.excerpt}}</p>
{% endfor %}
