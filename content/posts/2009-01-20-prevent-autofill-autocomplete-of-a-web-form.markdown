---
layout: post
title: Prevent Autofill / Autocomplete of a Web Form
date: 2009-01-20
---

If you have a web form and don't want a users input to be saved by a browser for repeated entry later then you need to employ autocomplete attribute of a form.

The HTML below will give you a simple form that the browser should not prompt the user to save the credentials.

You should note, this is not standards-compliant, but it does seem to work in chrome, IE and firefox.

{% gist 7840098 %}
