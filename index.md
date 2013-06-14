---
layout: default
title: "Thomas Boyt"
permalink: index.html
---

I'm a software engineer at [Tutorspree](http://tutorspree.com), [Hacker School](http://hackerschool.com) alumnus, and the creator of open source projects that include [Charcoal](https://github.com/thomasboyt/charcoal), [Ember Firebase Adapter](https://github.com/thomasboyt/ember-firebase-adapter), and [Noted](http://noted.herokuapp.com/).

You can find me on GitHub at [thomasboyt](https://github.com/thomasboyt) and on Twitter at [@thomasABoyt](http://twitter.com/thomasABoyt).

<h2>Blog</h2>
<ul class="posts">
    {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>
