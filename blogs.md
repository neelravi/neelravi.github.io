---
layout: post
title: Technology Tips
subtitle: A technology blog for niche tips 
last-updated: true
comments: true
#full-width: true
#cover-img: "/assets/img/landscape_cropped.jpg"
---

{% for post in site.posts %}
  <article>
    <h2>
      <a href="{{ post.url }}">
        {{ post.title }}
      </a>
    </h2>
    <time datetime="{{ post.date | date: "%Y-%m-%d" }}">{{ post.date | date_to_long_string }}</time>
    {{ post.content }}
  </article>
{% endfor %}