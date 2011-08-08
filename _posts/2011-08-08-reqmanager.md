---
layout: post
title: AJAX Request Manager for JQuery
---

When writing [Chromapaper][4], one of the biggest mistakes I made was not knowing how to properly manage lots of asynchronous XHTTP requests. Chromapaper used XHTTP requests to "scrape" both Instapaper's list of articles and the article pages themselves. However, I couldn't come up with a good, clean pattern for handling lots of asynchronous requests at once. What I wanted to be able to do was just say "for each article, download, then finish syncing". If you use a synchronous request, it is basically that simple, but it totally locks up the tab (or browser, if it lacks tab sandboxing) while it completes the requests.

I designed a library - I suppose that's what you'd call it, at least; it's only 45 lines (~1KB) of JavaScript - that makes it easy to handle lots of multiple asynchronous XHTTP requests. The key is that, **instead of just having a callback for a single request's completion, like a normal request, it also has a callback for when every single request is complete**. I used this library for an extension I designed for Chrome, [Something Awful Archiving Tool][1], which takes multi-page [Something Awful forums][3] threads and builds a single, easy-to-save-or-print page out of it. This extension has a request for each page for up to hundreds of pages, but doesn't build the page until they are all completed.

The library is dependent on JQuery for the AJAX requests. To use it, include it in a `<script>` tag before the rest of your JS (though after JQuery). Then, initialize a new request manager like this:

<script src="https://gist.github.com/1131628.js?file=init.js"> </script>

Once you've made the request manager, it's simple to add requests to it. Here's an example using the GitHub API to get the first ten pull requests from the JQuery project:

<script src="https://gist.github.com/1131628.js?file=createreq.js"> </script>

While it looks similar to a normal `$.ajax` request, it's different in that when the request is done, besides calling the callback, it also checks to see if other requests are done (`$.reqManager.requestComplete()`), and, if so, calls the `onFinished()` callback.

For another example design: In building a single page view for the SA Archiving Tool, I first created a new manager that had a `totalRequests` that equaled the (already-known) number of pages in the thread. I created a request for each page by iterating through the number of pages. The callback for each request saved the relevant data from each page needed to build a single page view. For the `onFinished` callback was simply set to the `buildPage()` function that then took the saved pages and created the view. 

I hope other people find this library as useful as I have. Much more elegant than synchronous requests, that's for sure.

**You can get the library at its GitHub page [here][2].**

[1]: https://github.com/thomasboyt/SA-Archiving-Tool
[2]: https://github.com/thomasboyt/JQuery-AJAX-Requests-Manager
[3]: http://forums.somethingawful.com/
[4]: https://github.com/thomasboyt/Chromapaper