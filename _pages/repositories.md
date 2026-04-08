---
layout: page
permalink: /repositories/
title: repositories
description: A curated showcase of my public GitHub work in computer engineering, MRI research, and hardware/software design.
nav: true
nav_order: 4
---

## GitHub Portfolio

This page highlights the GitHub projects that best represent my work in MRI reconstruction, FPGA design, and software analytics. I choose the repositories shown here deliberately so the page stays focused on projects that matter most to my portfolio.

To update the portfolio manually:

1. Open `_data/repositories.yml`.
2. Add your GitHub username under `github_users`.
3. Add the repositories you want to feature under `github_repos`, using the `owner/repo` format.
4. Commit and push the changes.

If the repository owner is also listed in `github_users`, the repo card will hide the owner name for a cleaner presentation.

{% if site.data.repositories.github_users %}

## GitHub users

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% for user in site.data.repositories.github_users %}
    {% include repository/repo_user.liquid username=user %}
  {% endfor %}
</div>

---

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
{% endif %}

{% if site.data.repositories.github_repos %}

## GitHub Repositories

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% for repo in site.data.repositories.github_repos %}
    {% include repository/repo.liquid repository=repo %}
  {% endfor %}
</div>
{% endif %}
