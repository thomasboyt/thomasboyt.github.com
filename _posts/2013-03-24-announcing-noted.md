---
layout: post
title: "Announcing Noted"
---

I've spent a significant portion of the last two months working on an app called [Noted](http://noted.herokuapp.com). Noted is an outline editor built for speed. It was inspired by my frustrating experiences with apps like Evernote and Notational Velocity, which, while powerful, were often cumbersome and difficult to manipulate - not attributes you want while trying to take notes in a lecture where the professor is talking much faster than you can keep up with!

Noted was built in Ember.js on top of Rails. It's been quite amusing to watch the [battle][ember1] [over][ember2] [Ember][ember3] play out on Hacker News, with many people deriding it for being impenetrable and "not ready" for applications to be built with it. Noted is an application that was *enabled* by the power of Ember. Yes, I fought long and hard with various aspects of it, particularly the Ember Data layer and the state errors it would throw out unexpectedly, but it was certainly a better experience than trying to write a complex view layer on top of Backbone or other more simple libraries for JavaScript MVC.

To be sure, there are quite a few [hacky][hack1] [bits][hack2] littered throughout the source code: dealing with the strange complexities of models that might or might not be loaded, routing that sometimes doesn't reset state to the degree you'd expect, and having [templates relying on complex state](https://github.com/thomasboyt/Noted-App/blob/master/app/assets/javascripts/templates/modals/dropbox/_dropbox_saving_modal.handlebars) in a templating language that doesn't offer more than a boolean "if" statement. But these "hacks" are no worse than the "hacks" you'll see in Angular JS or even some Backbone applications - sometimes it's just not worth finding the more elegant solution, as long as you leave the ugly one well-commented!

I built Noted at [Hacker School](http://hackerschool.com), which has been an absolute blast. It was wonderful to get constant, positive feedback, to have an immediate group to help test and give suggestions on usage of the app, and to be able to focus on *just coding* for the first time in my life, rather than trying to fit around traditional academia like I had been doing for the last two years. In fact, I'd tried to build this application before last year, but failed miserably because I couldn't find the time I needed to really learn how Ember worked (leading to an ill-advised custom build system and a horrible, over-complex architecture). 

This time, while I kept the base styling from a year ago (that is, gradients I eyedropped from Mac OSX window panes and a kind of ugly yellow), I went into this with the knowledge that I could spend time really learning Ember. I also found many aspects of the framework to be much more built-out than last time I had used it, with Ember Data's associations being much more elegant and Ember's routing not being a complex trainwreck. I also built it on Rails, which I'm primarily using as a powerful build system, thanks to the asset pipeline. While there are some Grunt plugins that can handle compiling Handlebars templates and you could shoehorn RequireJS into working with Ember, it's definitely much easier to "go with the flow" that Ember on Rails provides. I'll likely write more in detail about this in the future (though don't hold me to that).

I've built Noted to be a *useful application*, not just an exercise in learning. One of my biggest concerns was that notes are *important*, whether keeping track of a shopping list or needed to cram for an exam the next day, and that not being able to get to them would be a complete dealbreaker for a service. While I couldn't promise perfect uptime, I could promise two things:

* An offline-by-default mode that always worked, even if the server was entirely down
* Dropbox syncing in plain text files, so that even if you're stuck without a laptop, you can still read (or even edit, theoretically) your notes on the go

Thus, instead of rolling a custom back-end, I integrated with Dropbox. Not only that, but the Dropbox integration is entirely handled through the client-side with [Dropbox's JS library](https://github.com/dropbox/dropbox-js), so that if my server is entirely down, you can still export your notes to Dropbox. Between that and the offline-by-default abilities provided by [HTML5's Appcache APIs](https://developer.mozilla.org/en-US/docs/HTML/Using_the_application_cache), after you visit the site once, you basically never need to connect to it again (except for downloading updates).

I'm really proud of Noted. I've been using it myself for several weeks, and I'm glad that this application I've wanted to exist for a year exists and is useable. I hope you consider [using it too](http://noted.herokuapp.com).

[ember1]: https://errplane.com/blog/why-we-are-moving-away-from-emberjs
[ember2]: http://discuss.emberjs.com/t/getting-started-with-ember-js-is-easy-no-it-isn-t/559
[ember3]: http://wekeroad.com/2013/03/06/ember-confuses-me

[hack1]: https://github.com/thomasboyt/Noted-App/blob/master/app/assets/javascripts/controllers/dropbox_controller.js#L76
[hack2]: https://github.com/thomasboyt/Noted-App/blob/master/app/assets/javascripts/routes/application_route.js#L8

