---
layout: post
title: FluentMigrator Code Templates
date: 2011-05-17
---

A little while back I created some [migrator.net code templates](http://theoldsewingfactory.com/2010/09/30/migrator-net-code-templates/). Since I've now moved over to using [FluentMigrator](https://github.com/schambers/fluentmigrator/) it seemed only reasonable that I port the templates for the FluentMigrator project as well. To use them simply take the zip file from [here](https://github.com/ilivewithian/Fluent-Migrator-Code-Template/blob/master/Release/FluentMigratorTemplate.zip) and drop it into C:\Users\username\Documents\Visual Studio 2010\Templates\ItemTemplates\Visual C#\ Once you've done that restart Visual Studio and you'll be able to do File > New File > New Fluent Migration. Give the file a name and you'll get a code file a little like this:


    using System.Data;
	using FluentMigrator;
	namespace Domain.Migrations
	{
		[Migration(201105171040337)]
		public class MyFirstMigration : Migration
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


Which I happen to think is rather nice.
