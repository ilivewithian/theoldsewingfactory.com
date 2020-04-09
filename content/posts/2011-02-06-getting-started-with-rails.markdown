---
layout: post
title: Getting Started with Rails
date: 2011-02-06
---

I started in BASIC, moved to Java, then to .Net. After working in .Net for about 6.5 years it's time to properly learn something new. I've had my eye on learning Ruby on Rails for a while, but haven't ever really had a reason.

I still don't have a compelling reason, but I'm going to have a go at learning it anyway.

First things first, I need to get my Windows 7 machine ready to start developing. It turns out this is a really easy job.

1. Download the latest version of Ruby. I used a handy windows installer from here: [http://rubyinstaller.org/](http://rubyinstaller.org/)
2. Open a command prompt and type: *gem update --system*
3. Then once that's finished type: *gem install rails*
4. Now I need a folder to do my project work in: *mkdir MyFirstRailsApp* (on my desktop for now)
5. Switch to that directory: *cd MyFirstRailsApp*
6. Create a new project: *rails new MyFirstRailsApp*
7. Now switch to the directory rails just created for you and get the sqllite bits and bobs: *bundle install* (I'm not 100% sure that I had to run this command, but it certainly doesn't seem to have caused any problems)
8. Download the SQLite dlls from [here](http://www.sqlite.org/download.html) and drop them into you ruby/bin directory, mine is c:\Ruby192\bin\
9. Now you're ready to start your rails server, make sure you are in your project directory and type: *rails server*
10. If everything has gone correctly you should now be able to navigate to [http://127.0.0.1:3000/](http://127.0.0.1:3000/) and view your first Ruby on Rails application.

Look ma, I'm a ruby developer!

If everything in rails is as easy as this, I my find I go off .Net really quickly. Watch this space for me making a mess of learning how to actually do work in rails.
