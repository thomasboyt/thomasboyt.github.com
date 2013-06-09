---
layout: post
title: "Using Grunt & the ES6 Module Transpiler"
permalink: "es6-module-draft.html"
---

*(or tomorrow's syntax, today!)*

[Modules](http://wiki.ecmascript.org/doku.php?id=harmony:modules) are one of the most interesting and useful additions coming in ES6, the next version of JavaScript. A lack of universal module support has long been a pain point for JavaScript. While it's been understood for a long time that simply concatenating files together isn't going to cut it when you start developing complicated applications and dependency trees, the competing module specifications - AMD and CommonJS - are both difficult to use and difficult to make interop with each other.

ES6 modules will create a universal way of declaring modules in any context, whether running in the browser or on the server. But support for it is still a long ways off - the spec is still somewhat in flux,and it will be a while before we see proper support for it in either SpiderMonkey (Mozilla's JS engine) or V8 (Chrome and Node's JS engine).

The [ES6 Module Transpiler](https://github.com/square/es6-module-transpiler) lets you use the ES6 module syntax today. It outputs both AMD and CommonJS modules, meaning the output can be used in any JavaScript environment with minimal effort. However, rather than simply use the command-line interface for the transpiler, it's even easier to use [grunt-es6-module-transpiler](https://github.com/joefiorini/grunt-es6-module-transpiler) plugin.

## An example project

To demonstrate the transpiler, let's write a [very silly library](https://github.com/thomasboyt/es6-module-example) that contains two very silly methods: `shout(string)`, which returns the upper case string, and `ssshh(string)`, which returns the lower case string.

Our folder layout is fairly simple:

    lib/
        my_library/
            shout.js
            ssshh.js
        my_library.js
    Gruntfile.js
    package.json

### The source files

We create a module for each of our methods:

{% gist 5736652 shout.js %}

{% gist 5736652 ssshh.js %}

Then, we create a main/index module that will export our public interface:

{% gist 5736652 my_library.js %}

Notice that our `import` statements prefix paths with `./`. This is required for Node/CommonJS compilation to work correctly - `import { x } from "./example"` is compiled to `require("./example").x`, which will use the same pathing rules as `require` always uses. Dropping the `./` will cause Node to look up the module in `node_modules/`.

### Exporting AMD

Exporting AMD is easy:

{% gist 5736652 gruntfile-amd.js %}

We compile the amd files to `tmp/` because separate AMD files are generally not used in production. For distribution, we'll simply concat the files together:

{% gist 5736652 gruntfile-amd-concat.js %}

To be clear, this exported file will need an AMD module loader to work, whether RequireJS or Almond. We'll get to a browser-consumable version with no dependencies in just a second.

### Exporting CommonJS

Exporting CommonJS is also quite simple:

{% gist 5736652 gruntfile-commonjs.js %}

Here, we rename `my_library.js` to `main.js`, as is customary for the main module in a Node package. To match this, we'll add a `main` declaration to our `package.json`:

{% gist 5736652 package.json %}

Now this package can be easily consumed in Node like any other - just `npm install my-library` and `require("my-library")`.

### Exporting for the browser

While many people are using AMD or CommonJS packages in the browser through RequireJS or Browserify, a third version that simply exports to a global is recommended, as it's still the most common and simple way of including browser dependencies in a project.

The easiest way to export your library to a single global is to use your AMD output. To turn an AMD module into a global:

1. Wrap your AMD output in a closure, so that any other modules you load don't conflict with the names of your modules.
2. Add a module loader within this closure. The lightest one I've ever seen is [this tiny one by Yehuda Katz](https://github.com/thomasboyt/grunt-microlib/blob/master/assets/loader.js), used for this exact purpose. You can also use something more robust, such as [Almond.js](https://github.com/jrburke/almond), if desired.
3. Finally, within the closure, export your main module to a variable attached to `window`.

There's a simple Grunt task that can do all of this for you, also created by Yehuda Katz, located [here](https://github.com/thomasboyt/grunt-microlib/blob/master/tasks/browser.js). We can simply add a modified version of it to our Gruntfile, along with configuration for it:

{% gist 5736652 gruntfile-browser.js %}

Notice that `vendor/loader.js` - the same as the loader I linked above - is also included in the `src`.

You can see the final Gruntfile used for this [here](https://github.com/thomasboyt/es6-module-example/blob/master/Gruntfile.js), and the final output of this task [here](https://github.com/thomasboyt/es6-module-example/blob/master/dist/my_library.js).

## Wrapping up

Phew, that seems like a lot, doesn't it? But really, once you've got it configured once, you'll know how to do it again in the future. And the benefits are huge: besides being able to use future ES6 syntax, you now have builds of your library that work no matter what module system you or your dependents are using.
