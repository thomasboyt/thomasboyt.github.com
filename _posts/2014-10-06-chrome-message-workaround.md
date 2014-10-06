---
layout: post
title: "Chrome extensions: Handling messaging from injected scripts"
---

I spent my Saturday night, as I do many Saturday nights, screaming on Twitter about things being broken.

I've been working on a Chrome extension, [coquette-inspect](https://github.com/thomasboyt/coquette-inspect), which adds a debugger for [coquette.js](http://coquette.maryrosecook.com/) games to the Chrome Dev Tools. Chrome has had dev tools extension support for a couple years now, and I've used several awesome extensions in the past, so I figured it'd be a well-documented field.

So, you can imagine my surprise when I found that a really common use case was not only poorly-documented in the Dev Tools extension docs, but not documented *anywhere*, as far as I can tell.

## Background

Chrome Dev Tools extensions are a bit more complicated than "traditional" Chrome extensions. There are a few parts involved:

* The dev tools *panel* script, which handled the UI of the panel
* The *agent* script, which runs in the JavaScript context of the inspected page
* A *background* script, which was supposed to pass messages between the Dev Tools panel and the injected script (more on this in a second)

I needed to inject the agent into the context of the inspected page, which would then broadcast information about the game to the script that ran in the context of the Dev Tools panel.

Injecting a script is relatively easy. There's a nice API that can do this, `chrome.devtools.inspectedWindow.eval()`, which simply evals a string of JavaScript in the context of the current page. Add some XHR loading, and you've got yourself an injected agent script:

{% highlight javascript %}
// load injected script
var xhr = new XMLHttpRequest();
xhr.open('GET', chrome.extension.getURL('/agent.js'), false);
xhr.send();
var script = xhr.responseText;

// inject into inspectedWindow
chrome.devtools.inspectedWindow.eval(script);
{% endhighlight %}

Now, we've got our JS running in the context of the page, but we need to communicate with it.

The [Chrome Dev Tools extension guide](https://developer.chrome.com/extensions/devtools#content-script-to-devtools) has a code sample *seems* useful. It tells you how to communicate between a dev tools extension and a *content script*. A content script is similar to an injected script, but while it has access to the DOM of the inspected window, it doesn't have access to the JS context, so we couldn't use it instead of the `inspectedWindow.eval()` method. Still, they were similar enough that I naively assumed the solution for content scripts would work.

To my surprise, it, uh, didn't. [Here](https://github.com/thomasboyt/injectedWindow.eval-communication-sadness) is the repo I made demonstrating this. When the injected script uses Chrome's `chrome.runtime.sendMessage` method, it's not broadcast to the content script as it should be (and would be, with a content script).

## Solution

After two or three hours of cursing and trying different permutations of `sendMessage`, I, as usual, found salvation in someone else's solution.

[Backbone Debugger](https://github.com/Maluen/Backbone-Debugger) had a similar problem to mine, and its solution is really ingenious. Rather than directly trying to send a message from the injected script to the background script, it instead adds an [intermediary content script](https://github.com/Maluen/Backbone-Debugger/blob/fecd5ae9a4944afaf302a1c0ebce880c441a06af/js/contentscript.js). Now, instead of calling `chrome.runtime.sendMessage` in the injected script, it calls [`window.postMessage`](https://developer.mozilla.org/en-US/docs/Web/API/Window.postMessage). The message broadcast here is then picked up by the content script's event handler, which then broadcasts it to the background script.

The final data flow ends up looking like:

```
   injected agent
         |
         | window.postMessage()
         V
   content script
         |
         | chrome.runtime.sendMessage()
         V
 background script
         |
         | port.postMessage()
         V
dev tools panel script
```

[Here is the fixed version of the previous example code](https://github.com/thomasboyt/injectedWindow.eval-communication-sadness/tree/workaround), and the [diff](https://github.com/thomasboyt/injectedWindow.eval-communication-sadness/commit/55937ca04aed740ef0294c1113109b2e9e5724c7) of what changes were required.
