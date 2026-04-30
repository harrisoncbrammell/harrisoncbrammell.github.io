---
layout: page
permalink: /open-source/
title: open source
description: Free and Open Source Software is something I strongly believe in and hold close to heart. Below is a curated showcase of my public GitHub work in computer engineering, MRI research, and hardware/software design.
nav: true
nav_order: 4
---

My recent Github activity:

<div class="text-center mt-4 mb-4">
  <img src="https://ghchart.rshah.org/harrisoncbrammell" alt="harrisoncbrammell's GitHub contributions" class="img-fluid" />
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
