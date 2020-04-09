---
layout: post
title: An imperfect Hack to Keep App_Offline Showing When You Delete Your Web App
date: 2010-12-19
---

In a simple world, a good solution for deploying web apps looks a bit like this:


1. Remove server from load balancer
2. Remove old site
3. Install new site
4. Verify installation locally
5. Add server back into the load balancer

Nice and simple, now you just move onto the next server and repeat the process.

The problem I have is that I don't have that kind of access to the load balancer; I have no mechanism for taking a given server out of action other than using the app_offline.htm file. This works tolerably, but it's a dirty hack rather than an elegant solution. As part of my deployment process I always delete all files in the site, this is fine as all data is stored either in a database or in a different folder. This causes problems for asp.net as it stops seeing the site as an application and ignores the app_offline.htm file. That's no good for users as they will start to see either the default 404 page, or a message about the directory listing not being found. A simple hack that I've put in is to add the app_offline.htm to the list of default documents; also I set the IIS 404 for that site to app_offline.htm. It's safe to do this as my app sets its own 404 pages, so in normal operation the app_offline.htm is ignored. It's not a perfect solution, but it is better than nothing and hopefully brings the number of users seeing duff pages down to a minimum.

What I really need now is a way for the app_offline.htm file to be ignored for requests coming to the local machine and then I can stop doing deployments in front of my users.
