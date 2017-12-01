---
layout: page
title: Sitemap
permalink: "/sitemap"
short_summary: Blog posts by label

---

<div class="container">

  <div class="row">
    <div class="col-md-12">
      <h1>Blog posts by label</h1>
    </div>
  </div>

  <div class="row">
    <div class="col-md-6">
      <h2 id="android">Android</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'android' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="aws">AWS</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'AWS' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="bash">Bash</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'AWS' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="clojure">Clojure</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'clojure' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="docker">Docker</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'docker' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="git">Git</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'git' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>

    <div class="col-md-6">
      <h2 id="python">Python</h2>
      {% for post in site.posts %}
      {% for keyword in post.keywords %}
      {% if keyword == 'python' %}
      <p><a href="{{ post.url }}">{{ post.title }}</a></p>
      {% endif %}
      {% endfor %}
      {% endfor %}
    </div>
  </div>
</div>
