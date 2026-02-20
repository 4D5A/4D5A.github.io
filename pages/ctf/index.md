---
layout: page
title: "CTF Writeups"
permalink: /ctfwriteups/
css: /assets/css/ctf.css
full-width: true
---
<div class="ctf-grid">
  {%- comment -%}
  Automatically detect all pages in /ctf/ and create tiles with post counts
  {%- endcomment -%}

  {%- assign ctf_pages = site.pages | where_exp: "p", "p.url contains '/ctf/'" -%}

  {%- for event_page in ctf_pages -%}
    {%- assign event_posts = site.posts | where:"event", event_page.event -%}

    <a href="{{ event_page.url | relative_url }}" class="ctf-tile">
      <div class="ctf-thumb">
        {%- if event_page.thumbnail -%}
          <img src="{{ event_page.thumbnail | relative_url }}" alt="{{ event_page.title }}">
        {%- else -%}
          <div class="ctf-placeholder">No Image</div>
        {%- endif -%}
      </div>
      <div class="ctf-title">{{ event_page.title }}</div>
      <div class="ctf-count">{{ event_posts | size }} writeups</div>
    </a>
  {%- endfor -%}
</div>

<style>
.ctf-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  margin: 20px 0;
}
.ctf-tile {
  display: block;
  width: 250px;
  border-radius: 10px;
  overflow: hidden;
  text-decoration: none;
  color: inherit;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transition: transform 0.2s, box-shadow 0.2s;
}
.ctf-tile:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 16px rgba(0,0,0,0.3);
}
.ctf-thumb {
  width: 100%;
  height: 150px;
  background: #eee;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.ctf-thumb img { width: 100%; height: 100%; object-fit: cover; }
.ctf-placeholder { font-size: 1em; color: #555; }
.ctf-title { padding: 5px 10px; text-align: center; font-weight: bold; background: #f8f8f8; }
.ctf-count { text-align: center; font-size: 0.9em; color: #666; padding-bottom: 10px; }
</style>