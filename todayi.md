---
layout: default
title: "Thomas Boyt | Today, I"
permalink: todayi.html
---

# Today, I...

This is a public log of the work I do each day. 

Current projects: [Noted](https://github.com/thomasboyt/Noted), [Charcoal](https://github.com/thomasboyt/Charcoal)/[Ember-Boilerplate](https://github.com/thomasboyt/Ember-Boilerplate)

### April 29

* Refactored localStorage adapter for ember-data to be a little less... weird and slapdash, I guess. Still not quite satisfied with it. Also still haven't fixed the "updating parent associations causes view refresh" issue; may need to start delving into ember-data itself. Should ask around to make sure there's no alternatives first.
* Fixed less task in grunt-contrib to work for building multiple files into one (edge use case, since 99% of people would just use an @import directive instead of building two files into one, but doesn't hurt).

### April 28

* Ember templates in Charcoal are now added into the Ember.TEMPLATES array via a generated templates.js file instead of being embedded in the page.
* Assets task became more of a general-purpose copy task, may see about adding to grunt-contrib.
* Updated docs for Charcoal to reflect changes.
* Working on refactoring the localStorage adapter Noted uses to be more efficient & readable.

### April 27

* Removed less.js from the repo and instead started requiring my fork of [grunt-contrib](https://github.com/gruntjs/grunt-contrib), a collection of common Grunt tasks.
* Added compression information to grunt-contrib's minCSS task, similar to how the gruntjs min task works.
* Updated Ember-Boilerplate's grunt.js: Added default minCSS task (from grunt-contrib); Less support in the watch and build tasks is now added/removed based on whether configuration for the Less task exists
* Wrote docs & a blog post for Charcoal

### April 26

* Added a default minifcation task (for JS only) and a gitignore file to Ember-Boilerplate.
* Fixed and simplified the assets task in Charcoal.
* Researched more about potential module systems for Charcoal. RequireJS doesn't seem like a good fit for Ember, what with Ember using a global namespace for the app. There's workarounds, but they're a bit complex. Tom Dale makes [a good case](http://tomdale.net/2012/01/amd-is-not-the-answer/) against AMD in general. Really, with the way Ember works, what's needed isn't modules, but just a system for specifying *loading order*, like Rails' [Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives).

### April 25

* Cleaned up the LESS code in Noted in preparation for adding a full-screening ability.
* Updated my ember-data fork with the newest version of the localStorage adapter from Noted, since several people asked about it in IRC.
* My [grunt-contrib pull request](https://github.com/gruntjs/grunt-contrib/pull/2) got accepted & merged in.
* Started to look into alternative templating systems for page templates in Ember-Boilerplate, because Handlebars on top of Handlebars doesn't exactly work. Too bad you can't set alternative open/closing tags in Handlebars.

### April 24

* Created better error handling for Charcoal's LESS task. Since it's based off the same code that the grunt-contrib project's LESS task is, created a fork & pull request with the new code.
* Renamed Ember template tags in Charcoal. `emberTmpl` -> `ember_template` and `ember_named_template`.
* Renamed the `app/templates` folder in Ember Boilerplate to `app/pages`. Keeps it from being mixed up with `app/ember/templates` and is a more specific name for what it is.
