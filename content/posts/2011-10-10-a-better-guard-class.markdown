---
layout: post
title: A Better Guard Class
date: 2011-10-10
---

Recently I was working with some of the developers in a different office, I don't normally see their code, so it was interesting to see how they are meeting various problems. Generally speaking, they are doing some nice looking work. The architecture that they are moving towards looks like it's going a long way to solving a lot of problems, all good stuff.


One bit of code that I kept seeing really bothered me. It was a guard class that I kept seeing. I think it was a clone of a class from the <a href="https://gist.github.com/ayende/rhino-etl/blob/master/Rhino.Etl.Core/Guard.cs" title="Guard.cs" target="_blank">here</a>. The reason it bothered me was simply that it didn't read well, I kept thinking that it was guarding against an exception. In reality it was guarding against an error condition and would throw an exception if the condition happened. For example:

{{< gist ilivewithian 1273821 >}}

I would much rather something that reads like this:

{{< gist ilivewithian 1273854 >}}

Here's my implementation of the class, thoughts?

{{< gist ilivewithian 1273783 >}}
