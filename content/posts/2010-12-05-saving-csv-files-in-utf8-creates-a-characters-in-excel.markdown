---
layout: post
title: Saving CSV files in UTF8 Creates &#194; Characters in Excel
date: 2010-12-05
---

A few weeks ago in work I was minding my own business creating one of the ubiquitous Export to CSV features. You know the one, the one that screams "My application doesn't even get close to what my client needs, we've run out of time, CSV FTW!", yep, that one. Anyway, I ran my tests and checked the code in, happy that it did what it was supposed to.

Wrong.

I got a bug report from the client that the exported file had spurious &#194;, characters in it. Well, I ran the tool popped open the file in notepad++, nope, nothing in there. I ran the export with a larger dataset and tried again, ok now for a monster export, give me everything. Still no joy, so I did what the average developer is prone to doing and assumed that the client was being daft. I pushed back and asked for the client to give me an example. They did, I opened the file and nope, there wasn't a single &#194;, in the file. Ok, so either the client is having a laugh at my expense, they're lying or I'm missing something. Knowing this client, I'm missing something. I open the file again, but for a moment pretend I'm not a developer and open the file in Excel. &#194;'s abound, _arse_, how did I do that?!

It turns out that for all Excel's sophistication it doesn't like UTF-8 encoded files. It's much more at home with files encoded as Windows-1252, well at least for Latin languages; I'm not sure what happens in non-Latin languages.

So if you have code that looks like this:


    using (var writer = new StreamWriter("myfile.csv", false))
    {
        writer.WriteLine("Look,Ma,I,Can,Do,CSV");
    }

Change it to this:


    using (var writer = new StreamWriter("myfile.csv", false, Encoding.GetEncoding("Windows-1252")))
    {
        writer.WriteLine("Look,Ma,I,Can,Do,CSV");
    }

And now Excel will play nice. You can now rest easy in the knowledge that your client can use Excel to draw pretty graphs, pivot things and you know, do whatever it is normal people do with Excel.
