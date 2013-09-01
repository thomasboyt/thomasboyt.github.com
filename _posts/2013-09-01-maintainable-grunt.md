---
layout: post
title: "More maintainable Gruntfiles"
---

One of the awesome things about [Grunt](http://gruntjs.com/) is how easy it is to configure. Unlike the imperative tasks in a Rakefile or a Makefile, Grunt has a simple, declarative configuration. A Gruntfile is simply a JS file that loads tasks and sets up the configuration. A traditional Gruntfile, like this one from [Grunt's documentation](http://gruntjs.com/sample-gruntfile), looks like this:

{% gist 6406507 traditional_gruntfile.js %}

This is an awesomely powerful build setup - concatination, minifcation, testing, hinting, and auto-rebuilding - in only 50 lines of code.

Unfortunately, this approach doesn't always scale very well. As your application grows, you'll want to bring in more tasks, targets, and configuration. This can add up quickly, and you start seeing Gruntfiles that grow to hundreds of lines of code. Even worse is when custom tasks are mixed into the same file - usually as a tiny stopgap at first, but inevitably growing until the file is disorganized and hard to read.

Thankfully, there are ways to mitigate these issues!

## Load your tasks with a one-liner

In the example Gruntfile, our tasks are individually loaded with the `loadNpmTasks` method:

{% gist 6406507 load_npm_tasks.js %}

This is fine when you're using a handful of tasks, but is an annoying chore to keep up with as you scale. Thankfully, there's a handy little NPM package called (unsurprisingly) [load-grunt-tasks](https://github.com/sindresorhus/load-grunt-tasks) that can replace all of these lines with just one:

{% gist 6406507 load_grunt_tasks.js %}

This one-liner will automatically load any tasks you have listed in your devDependencies in your project's `package.json`, provided that the task's package name begins with the `grunt-` prefix.

## Use a tasks/ folder...

Generally, you should never define tasks (except for aliases, such as `test` and `default` in the example) in the Gruntfile itself. Instead, simply create a `tasks/` folder to contain your tasks, which should look something like this:

{% gist 6406507 example_task.js %}

Then, in your Gruntfile, just add this line to load your custom tasks:

{% gist 6406507 load_tasks.js %}

## ...and a tasks/options folder!

Now that your task loading and organization has been simplified, it's time to do something about your giant block of configuration. It's surprisingly easy to split this up into multiple files.

First, make a containing folder - I recommend `tasks/options` - and a file for each of your tasks (with the same filename as the task name, i.e. your `concat` task's configuration file should be named `concat.js`). The contents of each task are simply their exported configuration:

{% gist 6406507 concat.js %}

In your Gruntfile, add this utility function, which will `require` each of your configuration files and concatenate them into a single object:

{% gist 6406507 load_config.js %}

Finally, to use this function, just extend its return value into any generic configuration you might have, and call `initConfig` with your final configuration object:

{% gist 6406507 load_config_usage.js %}

## Hardcode as few values as possible

You might have noticed that the default configuration in the previous snippet included both `pkg`, the contents of `package.json`, and `env`, the current environment variables.

Generally, when you have any kind of option that could be toggled/changed by the developer - for example, a flag to enable a debug build, or a different API key for deploying - you should make that configurable through an environment variable, so the developer doesn't have to modify the task configuration directly.

`package.json` configuration is useful for being able to use default boilerplate (such as the uglify task's banner in the initial example) and not having to update your configuration if something basic (like your project's name) changes. You can also feel free to add custom keys to `package.json` to use in your configuration.

## Conclusion

Hopefully, these tricks will make it easier for you to make maintainable Grunt configurations. If you'd like to see an in-action example, all of these ideas are implemented in [Ember App Kit](https://github.com/stefanpenner/ember-app-kit) (thanks [Stef](https://github.com/stefanpenner/) for introducing me to most of them!).
