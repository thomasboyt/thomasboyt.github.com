---
layout: post
title: "Rewriting Chromapaper"
---

Last Decemember, I got a CR-48 in the mail from Google. This was my first laptop ever, and I was excited by the prospects of mobile computing that everyone else has experienced: working and reading at school, on the bus, wherever.

[Chromapaper](https://chrome.google.com/webstore/detail/lpjpjcgbkjefppoahpegfajifjdmcblb) was born out of this. I had a need - reading my Instapaper articles offline, from within ChromeOS - and I created an application to fufill it. I uploaded it to the Chrome Web Store on a whim, and soon after publishing it I suddenly found it publicized, with coverage on Lifehacker and elsewhere. I was stunned, not only that I had created an application that other people used, but by the fact that suddenly, I had *users.* People that relied on my application, people who donated money for its continuous development, and people who would email me for support and suggestions.

But there was a problem with this: I was in the middle of my senior year of high school; while I could slack off some, I didn't have the time to fully support this application. I pushed out some updates - for example, changing the name from "Instapaper for Chrome" to "Chromapaper" after a polite email regarding trademarks from Marco Arment - while at the same time struggling to find the time and energy needed to upkeep the application.

So, I've had it on the backburner for a while now. In the interim, I've learned more JavaScript, such as the wonders of jQuery, prototypical object-oriented programming in JS, and, most importantly, CoffeeScript, an alternative language that compiles into JavaScript.

Chromapaper, of course, was written before I knew about all this stuff. Hell, I started on Chromapaper before I even knew any JavaScript! Because of this, it's an ugly mess of code that, and I can't work on it in its current state.

So, beginning today (as school is finally winding down, and I have been accepted into a college), I'm going to start a full rewrite of Chromapaper, using what I've learned over the past few months. This includes jQuery, which should make scraping Instapaper's HTML a breeze, better object-oriented programming (such as a page "model" that can be saved, deleted, or modified with its own methods instead of passing it to a global function), and CoffeeScript.

Besides rewriting the code, I'm also planning on experimenting with new features. The two biggest ones are a new readability-view that does not rely on Instapaper's article view, thus taking the strain off of their servers (and ending those pesky "error 400s" once and for all), and optional support for Instapaper's new subscription-only API that would allow you to mark articles that you like, want to remove, or otherwise change offline and have them updated on sync.

I can't give a real timeframe for any of this - I've tried in the past, and they're never accurate. But, hopefully, I can have some sort of new version out by mid-Summer.