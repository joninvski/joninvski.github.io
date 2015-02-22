---
layout: page
title: Blog
permalink: "/blog/"

---

<section id="contact">
    <div class="container">
        <div class="row">
            <div class="col-lg-12">
                <div class="posts">
                    {% for post in site.posts %}	
                        {% if post.type == "blog" %}
                            <div class="blog-entry">
                            <h3>
                                <a href="{{ post.url }}">{{ post.title }}</a>
                                <small>
                                    {{ post.date | date: "%B %e, %Y" }} 
                                </small>
                            </h3>

                <div class="short-summary">
                    {{ post.short_summary }}
                </div>

                <a href="{{post.url}}">Read all</a>
                </div>
        {% endif %}	
        {% endfor %}	
        </div>
        </div>
    </div>
</section>
