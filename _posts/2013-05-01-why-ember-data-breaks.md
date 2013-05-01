---
layout: post
title: "How and why Ember Data breaks"
---

[Ember Data](https://github.com/emberjs/data) never gets the credit it deserves.

While Ember.js is called an "MVC" framework, it doesn't really have the "models" that one usually expects: Ember's "models" are simply objects with properties that can have two-way bindings with the controller and view layers. What Ember Data adds is the ability for these models to be saved or retrieved from a back-end, regardless of what that back-end is.

Having spent the last two weeks developing an [adapter for Ember Data](https://github.com/thomasboyt/ember-firebase-adapter), I've become far more familar with it than I'd ever intended. Because of a lack of API documentation, the only way to learn how to implement an adapter is to plunge into the source for existing ones, and when that fails, going to the source for the core of Ember Data itself.

While I enjoyed parts of working with Ember Data, I often felt the same way lots of new Ember users do: Ember Data is "broken." This feeling is rapidly becoming the prevailing opinion, and is at this point shaping the entire narrative of Ember. And when Ember Data "breaks," it often does so in confusing and unintuitive ways.

But most developers miss the truth about Ember Data: it is not simply a lot of abstraction boilerplate. If there's one takeaway that you get from reading through its source, it's that Ember Data is attempting to solve problems on the client-side that have, as far as I know, never been solved in a universal manner before.

And because of the difficulty of these problems, there's a lot of complexity involved, and a lot of moving parts. This article discusses how Ember Data breaks, why it breaks, and how to solve these issues.

## Where it Breaks

### The StateManager

Ember.js has a surprising secret buried within its API documentation: [`Ember.StateManager`](http://emberjs.com/api/classes/Ember.StateManager.html). Ember.js has an implementation of a finite state machine that can be mixed into (or used as a property of) any Ember object. In ye olde Ember (that is, before Ember 1.0.0-pre.3 came out last January), the Router was actually an implementation of this StateManager. Now, as far as I'm aware, you don't directly interact with anything in Ember that uses the StateManager.

In Ember Data, however, this is a different story. You interact with a StateManager almost any time you trigger or listen to an event on an Ember Data record. All records have [their own StateManager](https://github.com/emberjs/data/blob/master/packages/ember-data/lib/system/model/states.js) that dictates exactly what that record is able to do at any given moment.

While [most descriptions](http://en.wikipedia.org/wiki/Finite_state_machine) of a finite state machine are quite complex, the main takeaway is that a record is always in a certain state and can *transition* to various states based on events. This ability to transition depends on what states are defined as "exit" states for a particular state. For example, if a record was in the *created* state, it could transition to *saving* (when being committed to the server) but not directly to *saved* (as it hadn't yet been committed to the server).

Unfortunately, the state manager leads to some of the most cryptic and confounding errors users run into when first starting out with Ember Data. They usually look like this:

<p style="text-align:left">
<code>Uncaught Error: Attempted to handle event `loadedData` on <App.MyModel:ember424:1> while in state rootState.loaded.updated.uncommitted. Called with {}</code>
</p>

What this means is that you're trying to do something to the record that it can't do in its current state. This often happens when trying to update a record that's currently saving or when trying to render a property on an object that's been deleted. In this case, the model is currently in `rootState.loaded.updated.uncommited` - meaning that it has been *loaded* from the server, *updated* on the client-side and has yet to be *committed* - but it's attempting to load new data from the server, which it can't do while dirty (updated but unsaved).

To anyone used to the incredible lightness of Backbone's model layer or a similar library, the idea of a 700-line finite state machine being the basis of a model seems *insane* at first glance. I remember my first thought on seeing it was *why on earth would anyone need this for what I thought was just a fancy abstraction over `$.ajax`?*

Ultimately, Ember Data's StateManager is about saving you from yourself. It keeps you from making changes to your records that could end up being overriden by the server, or fail to sync back, or cause the record to become invalid. What separates Ember Data from something like a server-side ORM is that Ember Data's records are inherently *stateful* and need to be carefully managed to not cause race conditions or break parts of the application because an event fired when it couldn't be handled.

### The REST Aadapter

Ember Data is not a REST adapter.

I'm going to repeat that:

**Ember Data is not a REST adapter.**

Ember Data *has* a REST adapter, yes. It comes with a quite powerful one, in fact. But Ember Data, itself, is not a REST adapter.

See if any of these questions sound familar:

* Why doesn't Ember Data work with my existing REST back-end's architecture?
* Why can't I manually set AJAX parameters?
* Why can't Ember Data handle my web app's edge case?

The problem at the core of these questions is that Ember Data's public API *was not built to be REST-specific.* The concept of Ember Data is to provide a single, universal API for interacting with *any* data store that can store objects. It doesn't matter if you're talking to localStorage, to IndexedDB, to a REST API, to a WebSocket, or to another JavaScript API such as Firebase's, the API you interact with is going to be the same. Ideally, you could decide tomorrow that you want to get rid of your servers and instead store everything in the browser without changing more than a couple lines of code.

This modularity means that Ember Data cannot be built to handle edge cases. The good news is that there are solutions:

1. You can roll your own adapter. This is far less difficult than it sounds. It's certainly no worse than the usual overriding of `.sync()` you'd do on a Backbone model. And if your backend speaks JSON, then with Ember Data's JSON Serializer, any kind of (de)serialization is already handled for you.

2. You can just augment your usual Ember Data API usage with custom methods for dealing with edge cases outside of the context of Ember Data. For example, my Firebase adapter has some edge cases where, I'll admit, it can break down pretty spectacularly. But since I've exposed the Firebase API in the adapter, my app can directly use the Firebase API to do certain things instead of going through Ember Data. Similarly, a REST API can always be queried with an `$.ajax` call when needed.

3. You can use the new [Basic Adapter](http://emberjs.com/blog/2013/03/22/stabilizing-ember-data.html) to create custom sync methods for models - similar to how sync methods are defined in Backbone - that can take advantage of the serializers and loaders Ember Data has. This requires more boilerplate and less "magic," but may be easier depending on your use case.

### Relationships

You'd think, in 2013, we'd have solved the challenge of resolving model relationships. "Has many" and "belongs to" are concepts that we are all far-too-familar with, right?

But try abstracting those concepts over data stores that may implement them in *completely different* manners, and you'll suddenly realize why it's by far the most brittle aspect of Ember Data. A quick glance at [Ember Data's bugs](https://github.com/emberjs/data/issues?labels=bug&page=1&state=open) shows that, at least at the time I wrote this, the vast majority are related to relationships.

There are other frameworks that handle associations to varying degrees. In Backbone, a relationship can be as simple as a model with child models set as a property. But, as far as I'm aware, no other client-side framework attempts to solve, in a universal manner, loading and saving these relationships to the backend.

There are three major problems with relationships in Ember Data. First, they expect very specific information to work. For example, if you're loading a `hasMany` relationship from a model, that model will need to have a list of all the IDs for the relationship's resources - there's no way to load only a subset of a few IDs and add more later.

Second, relationships in Ember Data are often far *too* magical. The way a `hasMany` relationship is accessed in Ember is a wonderful cautionary tale of doing too much in an accessor: `fooModel.get("myHasManyRelationship")` will immediately set off a `find()` request for all the IDs the model loaded. This leads to strange behavior: for example, it's impossible to get the `isLoaded` property of a relationship without, in the process, loading it! It's also impossible to add a model to a relationship without loading the relationship - `fooModel.get("myHasMany").pushObject(myNewModel)` will load the relationship before the change is even committed.

And third, relationships can mean different things depending on the adapter. One of the most unintuitive aspects of Ember Data's handling of relationships is the behavior of `Model.find()` (aka `Model.findAll()`). This returns a record array for that model, which is good. But there's actually no `didLoad` event triggered for the record array. This is because not all adapters can have a concept of *complete* when loading all records in a resource - a WebSockets server, for example, may just send the records one by one instead of in a single response. On the other hand, `Model.find({})`, which delegates to `.findQuery()`, will have a `didLoad` event triggered, as it's expected that `.findQuery()` will always return a complete result.

## Is Ember Data "Broken?"

This might be a cop-out, but my honest opinion is "kind of."

Ember Data is a complex project built by absolutely brilliant engineers, and it shows. But it's also a massive project that is under active development that may just not be active enough for its scope, with only a handful of core developers. It has lots of unresolved issues, some of which are fundamental and long-standing (the pagination story, or lack of one, is by far the most depressing thing about the project).

There are multiple efforts to refactor *massive* chunks of Ember Data. In particular, Stefan Penner's ["promise entity" refactor](https://gist.github.com/stefanpenner/9ccb0503e451a9792ed0) will solve many of the issues surrounding the lack of error handling and difficulty of handling events. In addition, the new Basic Adapter attempts to solve the problems that many new users have implementing application-specific adapters. But I worry that these big projects are often opaque, developed by only a few people and hidden from public view until a random merge and announcement happens.

On the other hand, is Ember Data *usable?* Certainly! And by being aware of how, why, and when it breaks, you can easily build your application with it. I've [built](http://noted.herokuapp.com) [applications](http://emberfbchat.herokuapp.com/) with Ember Data that work. It's possible, it's not difficult, and it's often *magical* when it works, which is that special feeling that front-end development thrives off of.

The bigger issue: will the long-term development of Ember Data *harm Ember.js as a whole*? As I stated at the start of the article, the continued instability of Ember Data is shaping the narrative of Ember. This is because new users simply aren't aware of the full scope of the complexity that Ember Data abstracts away - it's hard to explain to a user why it doesn't "just work" like their 5-line `$.ajax` call.

To this end, the Ember.js team *needs* to get in front of this issue, and make it very clear (a) what Ember Data is, (b) what its current status is, and (c) what users can do to help. The documentation of the library also needs to improve (something that I've been working towards by adding enough YUIDoc declarations to the code to be able to build [limited documentation](http://www.thomasboyt.com/ember-data-docs/)). This is stuff that I'm sure the developers are aware of, and I'm sure they're working towards. But it's something that has to happen quickly, before the narrative spins completely out of control, and before users have moved onto more promising libraries.

In the meantime, if you'd like to contribute to Ember Data, the repository is [always open](https://github.com/emberjs/data). If you'd like to build an adapter for it, it's easy and relatively well-documented (the REST adapter is a great example, too). And if you'd like to build something with Ember, don't be afraid of Ember Data, but know its limitations.
