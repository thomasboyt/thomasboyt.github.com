---
layout: post
title: "No Fit State: Managing UI state in Flux"
---

When I embarked on a new front-end project, I was excited to get to try out [Flux](https://facebook.github.io/flux/), an architecture developed by Facebook to help manage application state. Flux enforces a *unidirectional data flow*, avoiding some of the pitfalls of "two-way" data binding that is common in most JavaScript applications.

Unfortunately, as I began to dig deeper into my application and create more complex UI flows, I ran into a big problem: Flux doesn't provide a clear way to structure UI state in your application. After asking around (the #reactjs channel on Freenode, by the way, is extraordinarily helpful), this seems to be a common issue.

## Flux in brief

Facebook's [overview](https://facebook.github.io/flux/docs/overview.html) of Flux does a better job explaining it than I could, so I won't attempt to give an in-depth overview of it. The most important thing to understand is the overall flow of data:

* Views trigger *actions*, which are just functions that interact with data in some way (for example, you might have an action that fetches data from an API).
* These actions trigger *events* with their resulting data.
* These events are handled by *stores*, which are best-thought of as big bundles of application state. Stores are *global singletons*, and are usually scoped to a particular concern of your app - for example, Facebook might have a `MessageStore`, a `UserStore`, and a `FeedStore`.
* The callbacks that stores register mutate the store's state, then trigger an `emit` event, which is listened to by views. This event is intended as the *only binding* between a store and a view. In a Flux+React application, a component will usually register to this event when mounted, and will re-render any time this event is triggered.

There are a [multitude](http://fluxxor.com/) [of](https://github.com/spoike/refluxjs) [implementations](https://github.com/jmreidy/fluxy), which all follow the same basic flow. Facebook also has [their own](https://github.com/facebook/flux) implementation, which contains only the event dispatcher used to connect actions to stores.

The most important thing to understand about Flux is that it enforces a separation between calling an action and handling its result. The result of an action has to go into a store's state - it's *never* directly communicated back to a view.

## The problem with stores

This separation leads to my biggest issue with Flux, which is best illustrated by example.

Imagine that you have a modal that allows a user to make a new post in a feed. Since you're using React, you implement this with a parent component that has a single state boolean (`postEditorIsOpen`) that determines whether modal is open or not. When a user opens the modal, this is set to true, and the view is re-rendered. So far, so good.

Now, imagine the user writes a post and clicks submit. This triggers an action in Flux that submits the post via an AJAX request. The request comes back a success, and the success callback triggers an event that the `FeedStore` is listening for, indicating the post was made and to insert it into the local feed cache.

The data is now updated, and everything's fine so far, but our modal is still open. It seems reasonable that when the post goes through, the modal should be closed too, so the user will see their post in the feed immediately.

But how do we close this modal in the Flux architecture?

## Solution A: Embrace global state

The most "Flux-ish" way to do this would be to actually move the `postEditorIsOpen` boolean from the component's state into the *store's* state. This would require two changes:

* Adding an action for when the user requests the modal to open (clicking "new post") that will trigger an event listened to by the store that will set `postEditorIsOpen` to true in the store's state.
* The post success handler in the store would update `postEditorIsOpen` to `false` before emitting the change event.

This pattern - moving UI state out of a component's state into the store - is immediately attractive, because it seems relatively easy to adapt view code to use, and it also allows us to stay within the bounds of Flux's data flow.

However, global UI state is dangerous (which is, in fact, one of the problems *React* tries to solve). Remember that stores are *global singletons*. In a single page app, if you navigate away from a page that uses a store and then return to it, the store's state will be the same as when you left it. Thus, if you leave the page with the modal open and return to it later, the modal would once again be open without the user prompting for it!

This can be solved by manually managing your global state - i.e., resetting the UI state in your store when the component is removed from the page. This is tricky and easy to forget or mess up, though.

## Solution B: Add a new channel of communication

The other option is to add another binding between the view and the store that will allow for arbitrary events to be listened to, rather than just the single `change` event. The way you do this likely depends on your Flux implementation:

* [Fluxxor](http://fluxxor.com/) uses EventEmitter as a mixin on its store objects, so you can emit and listen for arbitrary events out of the box.
* [Reflux](https://github.com/spoike/refluxjs)  uses event-emitting Action objects instead of a central dispatcher. Your components can bind listeners onto events emitted by these Actions, just like stores would.

In either case, you'd register listeners in your `componentWillMount` hook and deregister them in `componentWillUnmount`, like you would for any other event. These listeners could then update the component's internal state: for example, the post modal would set `this.state.postEdtiorIsOpen` to `false` in its callback.

## Conclusion

Neither of these solutions are ideal - one requires too much global state, while the other requires going around Flux entirely. I've been leaning towards solution B, as I feel that global UI state is generally something to be *avoided*, rather than *managed*, whenever possible. Since I'm already using Fluxxor, it's also relatively trivial to add new events to my stores.

This is opinion, of course; there's nothing about global state that is *inherently* bad. Pete Hunt points out:

<blockquote class="twitter-tweet" data-conversation="none" lang="en"><p><a href="https://twitter.com/thomasABoyt">@thomasABoyt</a> Global state isn&#39;t necessarily bad if it&#39;s coupled explicitly and written to in a predictable way.</p>&mdash; Pete Hunt (@floydophone) <a href="https://twitter.com/floydophone/status/511622565656526848">September 15, 2014</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

This may seem like a lengthy post for a small problem, but this is, in my opinion, the biggest issue with the current Flux architecture. Hopefully, a clear best practice will emerge for managing UI state without tangling yourself up in stores.
