---
layout: post
title: Unable to Update Wordpress Plugins Hosted in IIS 7
date: 2010-12-13
---

I've had a bit of trouble updating my Worpress plugins this evening, it's normally a 30 second job, but tonight things were being a bit tricky.



Initially I was seeing Akismet complaining that it couldn't create its plugin directory. Looking at the folder in `<website root>\wp-content\plugins\` I could see that the folder was already there. Assuming that the plugin updater was just being daft I thought I'd delete it myself and try again. Nope, no joy, the folder wouldn't budge. My first guess was that something in IIS was holding a reference to the folder and not letting go. The easiest (if somewhat brutal option) was iisreset, but that didn't help either.  The next thing I tried was to use the folder options and check its permissions and that's where thing started to get a bit odd. The GUI was unable to show me the permissions to the folder and also refused to allow me (running as an elevated user) to take ownership of the folder. So I ran up a command prompt (again with elevated permission) and pointed takeown.exe at the folder; the command ran and told me it had successfully given me ownership. Nice! On to deleting the folder and trying again, oddly, the folder wasn't there anymore. Giving me permissions was clearly too much of a humiliation for the folder, it had committed hara-kiri and was no more. Simply reinstalling askismet to the latest version left my server happy as can be and left me a little more confused as to how windows handles it's folders.
