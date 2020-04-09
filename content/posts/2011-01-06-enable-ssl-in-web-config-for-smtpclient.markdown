---
layout: post
title: Enable SSL in web.config for SmtpClient
date: 2011-01-06
---

If you want to use SSL when connecting to your email server and are using the SmtpClient from .Net 4.0 you can now set EnableSSL via the web.config file, which you couldn't do before.

I'm only posting this because the docs ([http://msdn.microsoft.com/en-us/library/w355a94k.aspx](http://msdn.microsoft.com/en-us/library/w355a94k.aspx)) for .Net 4 don't seem to bother mentioning it, which is a bit frustrating.

Now you can do this:

{{< gist ilivewithian 2688956 >}}
