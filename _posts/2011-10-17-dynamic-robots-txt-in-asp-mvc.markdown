---
layout: post
title: Dynamic Robots.txt in ASP MVC
---

I recently had the need to switch a robots.txt file dynamically, depending on whether a site was deployed to live or beta servers. The solution was very simple, ditch the real file and replace it with a controller action. This is a brief note on how I did this. I don't pretend this is an ideal solution, it was something I knocked together before I went out for dinner.

The solution I went for was in three parts: a web.config transform, routing and the controller itself.
The web config transform for my live site looks like this:
{% gist 1293678 %}
The route is equally simple:
{% gist 1293683 %}
And finally the actual controller:
{% gist 1293645 %}
And there we have it, my `robots.txt` file now is customised on a per deployment basis.