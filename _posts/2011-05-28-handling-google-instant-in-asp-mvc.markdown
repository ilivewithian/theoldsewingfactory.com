---
layout: post
title: Handling Google Instant in ASP MVC
---

I love google chrome, it's how browsers should be, it just works. The one thing I really like about it is the instant setting. The problem is that most sites don't really know the difference between a bad url and a url that is being typed.
Well, now you can and it's really simple.
The following extension method will let your controller know if it's a full request or simply a user busy typing:
{% gist 997098 %}
A very simple use of it would be something like this:
{% gist 997103 %}
Let me know if you use this is a more creative way.