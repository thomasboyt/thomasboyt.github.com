---
layout: post
title: "Announcing Charcoal, the easiest way to create Ember projects"
---

Getting started with Ember.js is hard. It's one thing to look at code samples, snippets, and toy applications stuffed into a single file. It's quite another to figure out how to structure a large-scale, modular application.

Enter [Charcoal](https://github.com/thomasboyt/charcoal). Charcoal is a generator for [Yeoman](http://yeoman.io) that creates project scaffolding for you. It will give you a great preconfigured Gruntfile, a [Bower](http://bower.io/) file with Ember dependencies set up, an easy-to-use dependency/module system for your scripts, and testing that works with no extra configuration.

Simply put, it's the easiest way to write Ember since Ember-Rails, except now it's backend-agnostic.

Charcoal has:

* **A project generator.** `yo charcoal` will get you set up with everything a modern Ember application needs to build. The default Gruntfile comes with pre-configured tasks that will make development a breeze.

* **Super-simple dependencies with [grunt-neuter](https://github.com/trek/grunt-neuter).** Dependencies between scripts in Charcoal are handled with simple `require('dir/script')` directives. It's as easy as Sprockets/Asset Pipeline's `//require` directives.

* **Testing out of the box.** Charcoal applications come with the [Mocha test framework](http://visionmedia.github.io/mocha/) and the [Chai assertion library](http://chaijs.com/), giving you freedom to write your tests in whatever style you want, whether BDD, TDD, or even QUnit-style asserts.

* **A Bower file for the easiest browser dependency management ever.** Want to upgrade to a new version of Ember? Just change the version in your Bower file.

* **Convenient, preconfigured multitasks.** `grunt server` will create a development server that will automatically recompile your files when they change (and even reload your browser), `grunt test` will run your Mocha tests in PhantomJS, `grunt test-server` will create a web server for running your tests in a browser, and `grunt build` will build your app for distribution.

* **Easy customization.** Since you're using Grunt, adding a new task is as simple as installing the dependency, creating configuration, and adding it to the above multitasks.

* **No weird external dependencies.** You're writing JavaScript, why the hell should you have to install Ruby?

* **[A development guide for new users](https://github.com/thomasboyt/charcoal/blob/master/app/templates/charcoal/readme.md)**, bundled with every project. No longer will anyone working on your repo have to fear figuring out your build tools.

To get started with it, install Yeoman and Charcoal:

<pre>
npm install -g yo
npm install -g generator-charcoal
</pre>

Create a new project folder:

<pre>
mkdir my_project && cd my_project
</pre>

Generate a new project:

<pre>
yo charcoal
</pre>

And test it out:

<pre>
grunt server
</pre>

You should see a working example application. Then, just follow the development guide (added to your project under `charcoal/readme.md`) to learn how to create applications with Charcoal.

Charcoal is on [GitHub](https://github.com/thomasboyt/charcoal) if you'd like to add changes or report bugs. I'm excited to see what you build with it!
