---
layout: page
title: Home Lab
permalink: /homelab/
subtitle:
css: /assets/css/homelab.css
full-width: true
---

<div class="homelab-grid">

  {%- assign categories_to_show = 
      "dns:DNS,firewalls:Firewalls,networking:Networking,lxcs:LXCs,vms:VMs" 
      | split: "," -%}

  {%- for pair in categories_to_show -%}
    {%- assign parts = pair | split: ":" -%}
    {%- assign cat_key = parts[0] -%}
    {%- assign cat_display = parts[1] -%}

    {%- assign cat_posts = site.categories[cat_key] | default: empty -%}
    {%- assign cat_count = cat_posts | size -%}

    {%- assign expected_image = "/assets/img/homelab/" 
        | append: cat_key 
        | append: ".png" -%}

    {%- assign cat_image = nil -%}

    {%- for f in site.static_files -%}
      {%- if f.path == expected_image -%}
        {%- assign cat_image = f.path -%}
        {%- break -%}
      {%- endif -%}
    {%- endfor -%}

    <a href="{{ '/homelab/' | append: cat_key | append: '/' | relative_url }}" class="homelab-tile">

      <div class="homelab-thumb">
        {% if cat_image %}
          <img src="{{ cat_image | relative_url }}" alt="{{ cat_display }}">
        {% else %}
          <div class="homelab-placeholder">No Image</div>
        {% endif %}
      </div>

      <div class="homelab-title">{{ cat_display }}</div>
      <div class="homelab-count">{{ cat_count }} projects</div>

    </a>

  {%- endfor -%}

</div>

<style>
.homelab-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  margin: 20px 0;
}

.homelab-tile {
  display: block;
  width: 250px;
  border-radius: 10px;
  overflow: hidden;
  text-decoration: none;
  color: inherit;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transition: transform 0.2s, box-shadow 0.2s;
}

.homelab-tile:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 16px rgba(0,0,0,0.3);
}

.homelab-thumb {
  width: 100%;
  height: 150px;
  background: #eee;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.homelab-thumb img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.homelab-placeholder {
  font-size: 1em;
  color: #555;
}

.homelab-title {
  padding: 5px 10px;
  text-align: center;
  font-weight: bold;
  background: #f8f8f8;
}

.homelab-count {
  text-align: center;
  font-size: 0.9em;
  color: #666;
  padding-bottom: 10px;
}
</style>