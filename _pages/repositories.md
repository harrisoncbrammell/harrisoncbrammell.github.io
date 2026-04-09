---
layout: page
permalink: /repositories/
title: repositories
description: A curated showcase of my public GitHub work in computer engineering, MRI research, and hardware/software design.
nav: true
nav_order: 4
---

I deeply value open-source software and am committed to actively contributing to the community. My experience spans a wide variety of fields and technologies, including computer engineering, hardware design, embedded systems, MRI research, and web development. 

My recent Github activity:
<div class="text-center mt-4 mb-4">
  <img src="https://grass-graph.app/images/harrisoncbrammell.png" alt="harrisoncbrammell's GitHub contributions" class="img-fluid" />
</div>

{% if site.repo_trophies.enabled %}
{% for user in site.data.repositories.github_users %}
{% if site.data.repositories.github_users.size > 1 %}

  <h4>{{ user }}</h4>
{% endif %}
  <div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% include repository/repo_trophies.liquid username=user %}
  </div>

---

{% endfor %}
{% endif %}

{% if site.data.repositories.github_repos %}

## GitHub Repositories

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% for repo in site.data.repositories.github_repos %}
    {% include repository/repo.liquid repository=repo %}
  {% endfor %}
</div>
{% endif %}
