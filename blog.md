---
layout: page
title: Blog
permalink: "/blog/"
short_summary: Tech blog about functional programming, containers and other cool geeky stuff.

---

<div class="container blog-all">
    <div class="row">
        <div class="col-md-2"></div>
        <div class="col-md-8">
            <div class="posts">
                {% for post in site.posts %}
                {% if post.type == "blog" %}
                <div class="blog-entry">
                    <h1 class="post-title">
                        <a href="{{ post.url }}">{{ post.title }}</a>
                    </h1>
                    <span class="post-date">{{ post.date | date: "%B %e, %Y" }}</span>
                    <article>
                        {{ post.short_summary }}
                    </article>
                    {% if post.keywords %}
                      <span class="keywords">Labels: 
                      {% for keyword in post.keywords %}
                        <a href="{{ site.url }}/sitemap.html#{{ keyword }}">{{ keyword }}</a>{% if forloop.last == false %},{% endif %} 
                      {% endfor %}
                      </span>
                      {% endif %}
                </div>
                <hr/>
                {% endif %}
                {% endfor %}
            </div>
        </div>
        <div class="col-md-2"></div>
    </div>
</div>
