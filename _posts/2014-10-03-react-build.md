---
layout: post
title: "React's flattened module tree"
---

React does a really interesting thing with its build process that I haven't seen elsewhere.

React uses what appears to be CommonJS syntax in its files. For example, in [ReactDOM.js](https://github.com/facebook/react/blob/master/src/browser/ReactDOM.js):

{% highlight javascript %}
var ReactDescriptor = require('ReactDescriptor');
var ReactDescriptorValidator = require('ReactDescriptorValidator');
var ReactLegacyDescriptor = require('ReactLegacyDescriptor');
var ReactDOMComponent = require('ReactDOMComponent');
{% endhighlight %}

However, in CommonJS, a non-relative import (that is, one that doesn't start with `./`) is supposed to indicate a *package*, not a module within the same package. For example, if I do `require('react')` in a traditional Browserify project, it'll look for a module in `node_modules/react`.

None of those imported modules are in separate packages, but are within the React repo. Not only that, but they're not even in the same folder - this file is in `src/browser/ReactDOM.js`, but ReactDescriptor is within `src/core/ReactDescriptor.js`.

I knew that React eventually used Browserify to build its global bundle, so I dug in a bit further and found that before that, the `jsx` Grunt task [builds each file to JSX and moves it to `build/modules`](https://github.com/facebook/react/blob/master/grunt/tasks/jsx.js). And when I say moved, I mean it ends up as a totally flat structure - `src/browser/ReactDOM.js` is built to `build/modules/ReactDOM.js`, and `src/core/ReactDescriptor.js` is built to `build/modules/ReactDOM.js`. The JSX task also adds a prepending `./` to imports to make them relative.

At this point, the React build process creates a Browserify bundle from `build/modules`. However, when published to NPM, `build/modules` actually becomes the `lib/` folder:

![](http://i.imgur.com/w7tH2Ci.png)

I can think of a few advantages to having this "flat" structure:

* Folder organization is independent of your the module system's structure, so you can move files around all you want without breaking anything
* In CommonJS, you avoid having complex relational lookups (e.g. no `require('../../../../core/ReactDescriptor.js')`)

I wonder whether the added complexity in the build step is because of those advantages, or whether Facebook has an internal build tool that uses this flat structure? I imagine it's the latter, since while the advantages are nice, they don't seem nice enough to add this extra complexity. Leaving the `./` off of each import path is also a sign that React wasn't originally written with "traditional" CJS in mind.

**Addendum:** Jonathan Buchanan [replied](https://gist.github.com/thomasboyt/965c6bf248734e93c725#comment-1311212) to the [original version](https://gist.github.com/thomasboyt/965c6bf248734e93c725) of this post. He used the same strategy for a [personal project](https://github.com/insin/reactodo), by combining [gulp-flatten](https://github.com/armed/gulp-flatten) and this code snippet:

{% highlight javascript %}
var b = browserify('./build/modules/app.js')
glob.sync('./build/modules/*.js').forEach(function(module) {
  b.require(module, {expose: module.split('/').pop().split('.').shift()})
})
{% endhighlight %}

The `expose` option exposes each of the modules in the flattened folder as a non-prefixed module (allowing you to `require('Foo')` instead of `require('./Foo')`).
