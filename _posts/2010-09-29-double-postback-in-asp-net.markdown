---
layout: post
title: Double Postback in ASP .Net
---

One of the things that I've seen in a number of different ASP .Net apps is a double post back. Once you get it on a page it seems to be almost impossible to work around. You'll get everything from optimistic concurrency exceptions, double orders, repeated emails - all kinds of rubbish.
It started happening on an app I'm currently developing, it's a new app and wasn't doing anything complicated so I started digging around in my code to see what was happening. It turns out the problem is nothing to do with the code I'd written per se. Rather the solution was as simple as it gets.

In my rendered html I had an image tag that looked a bit like this:
    <img src="" />
Which looks fairly innocuous, but what happens in some browsers is that they interpret the image source as the page itself. So strictly speaking you don't get a double postback, but rather a double request. Fixing the image links fixes the problem.
Hopefully that helps someone.