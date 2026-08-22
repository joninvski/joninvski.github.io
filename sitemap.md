---
layout: page
title: Sitemap
permalink: "/sitemap"
short_summary: Blog posts by label

---

{% assign topics = "android|AWS|bash|clojure|docker|git|python" | split: "|" %}

<div class="container">
    <section class="section-pad">
        <h1 class="section-title">Posts by topic 🗂️</h1>
        <p class="section-sub">All the rabbit holes I've fallen into, sorted.</p>
        <div class="blog-listing sitemap-cols">
            {% for topic in topics %}
            {% assign topic_id = topic | downcase %}
            <h3 id="{{ topic_id }}">{{ topic }}</h3>
            {% for post in site.posts %}
            {% for keyword in post.keywords %}
            {% if keyword == topic or keyword == topic_id %}
            <p><a href="{{ post.url }}">{{ post.title }}</a></p>
            {% endif %}
            {% endfor %}
            {% endfor %}
            {% endfor %}
        </div>
    </section>
</div>
