---
layout: post
title: "Quorum: A New Kind of Forum (draft)"
---

Once upon a time, forums were the go-to solution to building a general-purpose discussion community. Whether you needed to create a tech support area for customers of a product, a place to communicate directly with fans of a webcomic, or a discussion site for your Quake 3 clan, the easiest solution was a forum package such as phpBB or vBulletin.

Times have changed, though. Nowadays, it's just as easy to create a Facebook fan page for your announcements, a Twitter account for communication with fans, and handle your support through any number of question and answer services. In this brave new Web 2.0 world of ours, the classic web forum has lost much of its luster. 

But here's the big secret: *everything from Reddit to Quora is just an evolution of the forum.* Of course, they don't have the same layout forums have had since the days of UBB and Perl scripts. They're not set into a gridded index subforums, with the threads listed in descending order of most recent post. Instead, they both try to bring interesting content directly to you - Quora lets you follow individual topics (subforums) and questions (threads), while Reddit lets you follow subreddits and alerts you to new posts in comment threads. Really, these are just traditional forums, but organized differently.

So why are they different? The truth is _traditional web forums are outdated._ They don't expand easily, being defined into rigid subforums. Threads easily get lost in the fray, questions go unanswered, and huge, hard-to-follow threads can build up when a topic is just too big for one thread, but doesn't have a subforum. It's hard to find the most interesting and relevant content in traditional forums.

So, let's create a new kind of forum software for the modern era. Specifically, one that builds on concepts from sites like Quora (hence the name), Reddit, and more.

# Goals

* Be flexible. A good forum is multi-purpose. Quorum needs to be usable for the three major uses of forums:
	* Question and answer (Quora)
	* General discussion (most of Reddit)
	* Knowledge Base (Ubuntu Forums)
* Allow users to create topics, but keep focused on the site's niche
* Deliver the best, most relevant content to each user, while leaving open the option to browse a forum in full for interesting items.
* Reward users for contributing good content
* Organize content in a sensible, easy-to-understand way
* Tie into existing social networks

# Topics

Topics make it easy to find threads that interest you. Topics essentially work like tags on other sites, but should only be used to define the topics a thread involves - they shouldn't be used, for example, to mark a thread as "funny" or "one of Joe's favorite threads.""

Users are encouraged to make topics that don't exist, as long as they're not duplicates of existing ones or irrelevant to the forum's focus.
* A video game (*Bulletstorm*)
* A news event (March 2011 Japanese Earthquake)
* A person (Lady Gaga)
* A genre of music, movies, games, books... (sci-fi)
* A general type of thing (video game)
Just like Quora, each topic page that has a description editable by all (for example a page on *Bulletstorm* could list things like release dates, FAQs, etc.) that doubles as a subforum view.

Users are encouraged to tag their threads with as many topics as are relevant. Doing this helps users find the content they want. For example, a user could follow "PlayStation 3 games" or "First-Person Shooters" instead of "Video games" if they have specific interests. On the other hand, if they like video games, but don't care about sports games, they could follow "Video games" and filter out "Sports games."

Moderators have more advanced powers when it comes to topics. They can *star* topics, which marks them as essentially broad, key topics. To enforce discussion relevant to the scope of a forum, a forum can be configured to require posts have at least one of these "starred" topics, such as "Video games" or "Sports," attached to it.

# Threads

Threads are pretty simple. A thread is just a linear list of posts related to the topic.

Threads are actually *not* threaded - really, they should have a different name. They're linearly organized by time, like most forums (phpbb, vB) This might seem sort of crazy in a world used to Reddit comments and other similar sites, but it's for a few reasons-
* Keeps threads from spiraling out into megathreads - if you're really having such a complex discussion that you need it to be threaded, you should probably make a new one
* A chronological order makes it easy to follow a thread over time.

Threads can be followed and show up in your feed, just like topics.

# The Feed and the Thread List



# Misc
* BBCode is outdated! It's really annoying to use most of the time, and has been superseded by new markup languages and WYSIWYG editors. The new post and topic editor has two options: WYSIWYG and Markdown. Both can be customized to suit the needs of the forum. By default, the topic editor has more advanced features than the post editor, adding headings and colors.
* The forum automatically resizes images to fit within the width of the page. Clicking on larger images that are not within links will open the full-sized image in a new tab.