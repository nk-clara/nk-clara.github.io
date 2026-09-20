---
layout: default
title: Projects Directory
---
<h1>Project Portfolio</h1>
<p>Select a project below to view its details, stack architecture, and complete engineering log.</p>

<div class="project-grid">
    {% for project in site.projects %}
        <div class="project-card">
            <h2><a href="{{ project.url | relative_url }}">{{ project.title }}</a></h2>
            <p>{{ project.description }}</p>
            <a href="{{ project.url | relative_url }}" class="link-btn">Open Project Hub</a>
        </div>
    {% endfor %}
</div>
