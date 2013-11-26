---
layout: default
title: bpruitt-goddard.github.io
---

<ul>
  <li>11 November 2013 <a href= "preso.html">Pragmatic Programmer Slideshow</a></li>
  {% for post in site.posts %}
    {% unless post.classes contains 'slide' %}
    <li>
      {{ post.date | date: "%-d %B %Y" }} <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endunless %}
  {% endfor %}
</ul>