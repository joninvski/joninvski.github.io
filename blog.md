---
layout: page
title: Blog
permalink: "/blog/"
short_summary: Tech blog about functional programming, containers and other cool geeky stuff.

---

<div class="container blog-all">
    <section class="section-pad">
        <h1 class="section-title">Welcome to my brain dump ✍️</h1>
        <p class="section-sub">Functional programming, containers, and other geeky adventures.</p>
        <div class="blog-listing">
            {% for post in site.posts %}
            {% if post.type == "blog" %}
            <div class="blog-card">
                <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
                <span class="post-date">{{ post.date | date: "%B %e, %Y" }}</span>
                <article>
                    {{ post.short_summary }}
                </article>
                {% if post.keywords %}
                  <span class="keywords">
                  {% for keyword in post.keywords %}
                    <a href="{{ site.url }}/sitemap.html#{{ keyword }}">{{ keyword }}</a>
                  {% endfor %}
                  </span>
                  {% endif %}
            </div>
            {% endif %}
            {% endfor %}
        </div>
    </section>
</div>
