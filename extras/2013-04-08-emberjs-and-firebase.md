---
layout: post
title: "Using Ember.js & Firebase for powerful real-time applications"
permalink: "firebase-draft.html"
---

**TL;DR:** Check out this chat example, featuring authentication, message persistence, and username storage, done in ~ 130 LOC:

<iframe src="http://emberfbchat.herokuapp.com" seamless="seamless" frameBorder="0" width="610" height="400"></iframe>

This chat was developed using my own [Firebase adapter for Ember Data](https://github.com/thomasboyt/ember-firebase-adapter). The source is available [on GitHub](https://github.com/thomasboyt/ember-firebase-chat) (and if you're not familar with the Rails app structure, the Ember app itself lives [here](https://github.com/thomasboyt/ember-firebase-chat/tree/master/app/assets/javascripts)).

## Why Firebase?

[Firebase](https://firebase.com) is one of the most impressive technologies I've had the pleasure of using in the past few months. It's a hierarchial data store-as-a-service built to be the back-end for web and iOS applications. While I usually don't like to hitch a wagon to a back-end I don't control, I was impressed enough by Firebase that I decided to give it a shot.

What I found was that the real power lied not in the back-end itself, but in Firebase's [JavaScript API](https://www.firebase.com/docs/javascript). Rather than simply acting as an interface for REST requests via AJAX, Firebase's Javascript API offers events for when items are added, changed, and removed, powered by a persistent WebSocket connection. 

Immediately, I saw the potential that Firebase had when used as a back-end for Ember. By adapting Firebase for use with Ember's data persistence library, Ember Data, I could create models and queries that could update in real time, and require no extra boilerplate to handle add and remove events.

## The Chat App

The chat app is a bit of a cliche example of real-time applications at this point, but it's a good starting point. This one also shows off a few of the advantages of Firebase: it uses Firebase's authentication API to keep track of users, and all messages are stored within a Firebase reference for later viewing.

Let's step through it, file by file.

### Models & Store

Before diving into the program flow, take a look at the super simple models:

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=models.js"></script>

Note that they extend `DS.Firebase.Model`, not the standard `DS.Model` class.

The store is created like any other Ember Data store, with the current API revision and an adapter instance:

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=store.js"></script>

The `dbName` property is the subdomain of your Firebase.

### Router & Controllers

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=application_route.js"></script>

The router here is the only entry point into the application. Anyone can see the chat without logging in, so we go ahead and load all of the current chat messages with a `.find()` call to that resource.

This is where the Firebase magic happens: *any `find()` call with no argument is interpreted as a live-updating resource*. This find call will not only find all of the current messages (stored in `/messages/<id>`), but will also update automatically when new messages are added or when messages are removed.

The `chatController` the router creates is simple, mainly handling new messages:

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=chat_controller.js"></script>

You'll notice, though, that it also synthesizes the singleton `AuthController` with the [`needs` property](http://emberjs.com/guides/controllers/dependencies-between-controllers/). This controller handles authorizing users through Firebase's [Simple Login API](https://www.firebase.com/docs/security/simple-login-overview.html):

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=auth_controller.js"></script>

This is a little messy, but essentially does the following steps:

1. When the `AuthController` instance is created by the `ChatController`, it creates a `FirebaseAuthClient` to see if the user is currently authorized. 
1. The callback that this object calls is triggered any time a login is attempted, and creating the object actually triggers a login attempt, as to check for an existing session.
1. If the user is not authorized, it will display a login button in the view (which checks that `authed` is equal to false). Logging in with this button will open a Persona login prompt, which will attempt a login when completed.
1. If the user is authorized, the controller checks to see if there is a `/users/<auth id>` resource that has a set nickname.
1. If the nickname exists, `hasName` will be set to true, and the view will display the message entry prompt. If not, the view will instead display a prompt asking for a name. This prompt will call the `pickName` method, which will save the name back to the server.

### Views & Templates

Finally, the views that display everything are relatively simple. Here's the single template for the entire application:

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=chat.handlebars"></script>

Messages are iterated over just as you would any other controller's content. Again, though, new messages will be automatically added to the controller's content, and thus the template will update as new messages are posted.

The bottom `InputView` has a bit of code to facilitate picking names and sending messages, but is pretty much self-explanatory:

<script src="https://gist.github.com/thomasboyt/49a5f161d11beae6c430.js?file=input_view.js"></script>

And that essentially wraps up the code involved. There's a few other files in [the repo](https://github.com/thomasboyt/ember-firebase-chat/tree/master/app/assets/javascripts), but nothing else with any real content to it.

## Wrap-Up

This is a really simple example, and only shows off a tiny subset of what Firebase can do. Using [the Ember Firebase adapter](https://github.com/thomasboyt/ember-firebase-adapter), you can also:

* Create models with live-updating properties. Imagine a collaborative to-do list where an item is updated automatically as you and your collaborators change it.
* Define *live relationships* on models. You could extend this application to include multiple *chat rooms*, each with their own *messages*. By defining a `hasMany('Message')<->belongsTo('ChatRoom')` relationship, you could simply bind your view's content to a chat room's `messages` resource.
* Query for both *relational* resources and *embedded* resources. You can use Firebase as either a hierarchial data store (i.e. a message would be at `/chat_rooms/<chat id>/messages/<message id>`) or as a relational data store (a chat would be at `/chat_rooms/<chat id>/` and its messages would be under `/messages/<message_id`, with a `messages` attribute containing only the IDs of the messages in the chat).
* Use Firebase as a *key-value* store instead of simply pushing messages in with auto-generated IDs by specifiying the `id` property of a model when creating it.

...and more.
