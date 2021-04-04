---
layout: page
title: Publications
subtitle: List of peer-reviewed publications in reverse chronological order
last-updated: true
years: [2021,2020,2018,2017,2016,2015,2014,2013,2012]
---

{% for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -q @*[year={{y}}]* %}
{% endfor %} 
 

