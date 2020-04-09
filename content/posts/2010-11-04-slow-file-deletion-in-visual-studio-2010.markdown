---
layout: post
title: Slow file Deletion in Visual Studio 2010
date: 2010-11-04
---

I've recently been trying to delete some cruft from a few projects I'm working on. In one solution with about 16 projects I was trying to remove about 40 or so image files. It was taking a small eternity. I suspected that it was our flaky in house install of TFS, or maybe Resharper or maybe even a T4 template going crazy. I tried eliminating all of these and it was still very, very slow.

After a bit of digging around I found this posting [http://tech.groups.yahoo.com/group/altdotnet/message/11685](http://tech.groups.yahoo.com/group/altdotnet/message/11685) which basically suggests that if your recycle bin is getting near its limit and is set to be quite large it's going to take ages to remove files.

After emptying the recycle bin I went from 90 seconds to delete a file (yes 1 file, 90 seconds) I can now delete files almost instantly.

Problem solved, back to work.
