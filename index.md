---
layout: default
title: "Thomas Boyt"
permalink: index.html
---

I'm a software engineer, JavaScript afficianado, [Hacker School](http://hackerschool.com) alumnus, and a contributor to open source projects such as [Ember.js](http://emberjs.com), [Ember App Kit](https://github.com/stefanpenner/ember-app-kit), and [es6-module-transpiler](https://github.com/square/es6-module-transpiler).

You can find me on GitHub at [thomasboyt](https://github.com/thomasboyt) and on Twitter at [@thomasABoyt](http://twitter.com/thomasABoyt).

<h2>Blog</h2>
<ul class="posts">
    {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
