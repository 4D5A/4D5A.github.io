---
layout: page
title: "Labs"
permalink: /labs/
css: /assets/css/labs.css
full-width: true
---
<div class="labs-grid">
  {%- comment -%}
  Automatically detect all pages in /labs/ and create tiles with post counts
  {%- endcomment -%}

  {%- assign labs_pages = site.pages | where_exp: "p", "p.url contains '/labs/'" -%}

  {%- for event_page in labs_pages -%}
    {%- assign event_posts = site.posts | where:"event", event_page.event -%}

    <a href="{{ event_page.url | relative_url }}" class="labs-tile">
      <div class="labs-thumb">
        {%- if event_page.thumbnail -%}
          <img src="{{ event_page.thumbnail | relative_url }}" alt="{{ event_page.title }}">
        {%- else -%}
          <div class="labs-placeholder">No Image</div>
        {%- endif -%}
      </div>
      <div class="labs-title">{{ event_page.title }}</div>
      <div class="labs-count">{{ event_posts | size }} labs</div>
    </a>
  {%- endfor -%}
</div>

<style>
.labs-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  margin: 20px 0;
}
.labs-tile {
  display: block;
  width: 250px;
  border-radius: 10px;
  overflow: hidden;
  text-decoration: none;
  color: inherit;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transition: transform 0.2s, box-shadow 0.2s;
}
.labs-tile:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 16px rgba(0,0,0,0.3);
}
.labs-thumb {
  width: 100%;
  height: 150px;
  background: #eee;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.labs-thumb img { width: 100%; height: 100%; object-fit: cover; }
.labs-placeholder { font-size: 1em; color: #555; }
.labs-title { padding: 5px 10px; text-align: center; font-weight: bold; background: #f8f8f8; }
.labs-count { text-align: center; font-size: 0.9em; color: #666; padding-bottom: 10px; }
</style>