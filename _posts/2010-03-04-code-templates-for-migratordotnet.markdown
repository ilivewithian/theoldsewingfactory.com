---
layout: post
title: Code Templates for MigratorDotNet
---

**UPDATE:** A newer version of this code is available [here](/2010/09/30/migrator-net-code-templates/)
I've been hacking around with database migrations and decided to give <a href="http://code.google.com/p/migratordotnet/">MigratorDotNet</a> a go. The first problem I found was that I had to manually number the migrations. The trick of using the migration attribute with the current date and time formatted as `yyyyMMddhhssfff` works a charm, but is a pain to type more than once.
So to solve the problem I've created a code template that gives you something like this as a shell:
<pre lang="csharp">using System.Data;
using Migrator.Framework;
namespace MigrationLibrary
{
    [Migration(201003040752265)]
    public class Migration1 : Migration
    {
        public override void Up()
        {
            throw new System.NotImplementedException();
        }
        public override void Down()
        {
            throw new System.NotImplementedException();
        }
    }
}</pre>
To install it you need to do a couple of things, take the dll from in <a href="http://www.mediafire.com/?qjtyiuekzmv">here</a> and register it in your GAC and then take <a href="http://www.mediafire.com/?yjgqmizcmmm">this zip</a> and drop it into `\Visual Studio 2010\Templates\ItemTemplates\Visual C#`. Restart Visual studio and you can now add a migration from the new item menu.
I've only tried this in visual studio 2010 and have done no real testing. License wise, let people know I wrote it. If it breaks your machine, steals your financial identity, runs off with your wife and then crashes your car into a wall, it's your own fault for running code you found on the internet. You have been warned.