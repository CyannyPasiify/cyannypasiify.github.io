---
layout: archive
title: "网页存档"
permalink: /sitemap/
author_profile: true
---

{% include base_path %}

本页提供网站中全部页面的链接列表。摘录清单可参见[XML]({{ base_path }}/sitemap.xml)。

<h2>杂项页面</h2>
{% for post in site.pages %}
  {% include archive-single.html %}
{% endfor %}

<h2>博客</h2>
{% for post in site.posts %}
  {% include archive-single.html %}
{% endfor %}

{% capture written_label %}'None'{% endcapture %}

{% for collection in site.collections %}
{% unless collection.output == false or collection.label == "posts" %}
  {% capture label %}{{ collection.title }}{% endcapture %}
  {% if label != written_label %}
  <h2>{{ label }}</h2>
  {% capture written_label %}{{ label }}{% endcapture %}
  {% endif %}
{% endunless %}
{% for post in collection.docs %}
  {% unless collection.output == false or collection.label == "posts" %}
  {% include archive-single.html %}
  {% endunless %}
{% endfor %}
{% endfor %}
