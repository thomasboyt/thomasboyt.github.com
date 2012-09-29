---
layout: post
title: Interprocess Communication with Redis Pub/Sub
---

I was recently working on a small hobbyist project - [an IRC bot that notifies you by SMS when your name is mentioned](http://github.com/thomasboyt/sms-highlight-bot) - when I came across a problem I couldn't quickly solve. This is always the most exciting part of a personal project: an opportunity to learn a new technique, tool, or strategy for solving a problem. 

In this case, the issue was interprocess communication. I wanted to allow users to reply to the users who mentioned them in IRC via SMS. To do this, the small [Flask](http://flask.pocoo.org/) application that handles SMS replies (which are POSTed to it from [Twilio](http://twilio.com)) needed to be able to send a message to the IRC bot (a separate process), saying "user A is replying to user B with this message." The bot should receive this information and then send a private message to user B.

Interprocess communication is a problem that seems like it should be much more simple than it actually is. Python lists [a variety of modules](http://docs.python.org/library/ipc.html) that can be used for interprocess communication, many of which can also be used for networking. However, none of these seemed to be what I wanted: a simple, asynchronous way to send and receive messages.

I was attracted to Redis mainly because of how incredibly simple it is to set up and use. As a key/value store, there are of course no schemas, tables, or anything else to set up. Literally all that needs to be done is install - as easy as `$ brew install redis` on OSX - and connect. Although I already was using one SQL database to keep track of users (their usernames and phone numbers), adding Redis required almost no extra overhead or time spent setting it up.

Redis's power in interprocess communication lies in its [pub/sub capabilities](http://redis.io/topics/pubsub). Redis's pub/sub system is just as simple as the rest of Redis - clients subscribe to a "channel," or a stream of messages, and other clients can publish messages (which are merely strings) to these channels.

In my SMS project, the IRC bot will subscribe to a channel called "sms_replies," while the Flask application will publish a message to it every time it receives a reply. 

## Implimentation

To communicate with Redis, the IRC bot and the Flask app use the [redis-py](https://github.com/andymccurdy/redis-py) module. Redis-py is a very simple API interface, and simply translates the various Redis commands to database methods (i.e. `GET some_key'` becomes `redis_db.get('some key')`).

### Publisher (Flask app)

<script src="https://gist.github.com/3804817.js?file=pub.py"> </script>

The code to publish couldn't be simpler. Here, I've sent the message - containing the user it's from, the user it's to, and the reply itself - as a simple space-separated string, but you could send any sort of serialized data structure you'd like, if you need something more advanced.

### Subscriber (IRC bot)

<script src="https://gist.github.com/3804817.js?file=sub.py"> </script>

The most notable aspect of this code is that it uses a separate thread for listening to Redis, since the Redis listening loop blocks execution of the rest of the code.

## Conclusion

There's not much else in the way of details I have to offer. Redis is a rare piece of software that just works out of the box with zero configuration, and it's kind of amazing. Redis pub/sub is definitely not the only way to do interprocess communication, but it has a lot of advantages:

* Redis works on OSX and Linux with no configuration in development (and possibly even production!). While the redis-win32 port is unofficial and not production-ready, it's fine for development & hobby projects if you prefer to work on Windows.
* Redis is language-agnostic and has libraries for [just about everything](http://redis.io/clients)
* Redis is just as simple to impliment as it is to setup - just use `publish` commands and a listening thread (or similar - in node.js, it's [as easy as you'd expect](https://github.com/mranney/node_redis#publish--subscribe))