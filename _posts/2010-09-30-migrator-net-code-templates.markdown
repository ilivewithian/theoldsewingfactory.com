---
layout: post
title: Migrator .Net Code Templates
---

A little while ago I posted some code samples that made the process of creating migrations in migrator .net a bit more simple. Well it came with the "it works on my machine" seal of approval and indeed it worked on my machine, but not on others.  It turns out there is a difference between the way VS 2010 Express and VS 2010 Ultimate process code templates.
Anyway, I've found the bug and fixed it. You can download the latest version of the code from here: <a href="http://github.com/ilivewithian/Migrator-.Net-Project-Templates">http://github.com/ilivewithian/Migrator-.Net-Project-Templates</a>. If you don't want to compile the source your source yourself, don't worry there is a release folder in the root of the project with a compiled version of the project available.
To install the templates simple take the "Migrator .Net Templates.dll" and register it in the GAC and take the "MigrationTemplate.zip" file and drop it into your `<Documents>\Visual Studio 2010\Templates\ItemTemplates\Visual C#` folder. Now all that's left to do is restart Visual Studio and you are up and running.
To check it's up and running go to file new item and select migration template. Give it a name that's sensible and you should get something like this:
using System.Data;
using Migrator.Framework;
    namespace MigrationLibrary
    {
        [Migration(201009302115255)]
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
    }
Let me know what you think.