---
layout: post
title: Enable SSL in web.config for SmtpClient
---

If you want to use SSL when connecting to your email server and are using the SmtpClient from .Net 4.0 you can now set EnableSSL via the web.config file, which you couldn't do before.
I'm only posting this because the docs (<a href="http://msdn.microsoft.com/en-us/library/w355a94k.aspx">http://msdn.microsoft.com/en-us/library/w355a94k.aspx</a>) for .Net 4 don't seem to bother mentioning it, which is a bit frustrating.
Now you can do this:
{% gist 2688956 %}
