---
layout: page
title: projects
permalink: /projects/
description: KETI에서 담당한 로보틱스 프로젝트와 입사 전 학부·인턴 프로젝트. 카드를 누르면 배경·역할·접근·결과를 볼 수 있습니다.
nav: true
nav_order: 2
display_categories: [company, personal]
horizontal: false
---

<!-- pages/projects.md -->
<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {% for category in page.display_categories %}
  {% if category == "company" %}
    {% assign category_title = "KETI 담당 프로젝트 (2023.05 ~ 현재)" %}
  {% elsif category == "personal" %}
    {% assign category_title = "입사 전 프로젝트 (학부 · KIRO 인턴)" %}
  {% else %}
    {% assign category_title = category %}
  {% endif %}
  <a id="{{ category }}" href=".#{{ category }}">
    <h2 class="category">{{ category_title }}</h2>
  </a>
  {% assign categorized_projects = site.projects | where: "category", category %}
  {% assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal %}
  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
  {% endfor %}

{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-3">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>
