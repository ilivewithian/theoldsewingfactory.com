---
layout: post
title: Timestamp Your Assembly Version
date: 2011-02-27
---

Recently Jimmy Bogard ([@bogardj](http://twitter.com/bogardj)) blogged about a simple, but very effective versioning strategy for .Net assemblies. I thought I'd show you how I implemented that for a project I'm working on.

I opted to use MSBuild Community Tasks, which can be downloaded from [here](http://msbuildtasks.tigris.org). To get started, crack open your .csproj file and find the line that starts with “<Import Project”, there might be several of them, just find the last one. Underneath the last one add the line:

    <Import Project="$(MSBuildExtensionsPath)\MSBuildCommunityTasks\MSBuild.Community.Tasks.Targets" />

This just lets MSBuild that we are going to use the targets files that come with MSBuild Community Tasks. Next we need to wire in our new assembly info file, the current on will be replaced with the output of the build task.

A pretty much complete version follows immediately, but if you want it explaining a bit; keep reading.

    <Target Name="Version">
    	<Time Format="yyyy">
    		<Output TaskParameter="FormattedTime" PropertyName="year" />
    	</Time>
    	<Time Format="MM">
    		<Output TaskParameter="FormattedTime" PropertyName="month" />
    	</Time>
    	<Time Format="dd">
    		<Output TaskParameter="FormattedTime" PropertyName="day" />
    	</Time>
    	<Time Format="hhmm">
    		<Output TaskParameter="FormattedTime" PropertyName="time" />
    	</Time>
    	<Message Text="Version: $(year).$(month).$(day).$(time)"/>
    	<AssemblyInfo CodeLanguage="CS"
    		OutputFile="Properties\AssemblyInfo.cs"
    		AssemblyTitle="JacsWebsite"
    		AssemblyDescription=""
    		AssemblyCompany="http://www.jacquelinewhite.co.uk/"
    		AssemblyProduct="JacquelineWhite.co.uk"
    		AssemblyCopyright="Copyright © Jacqueline White 2011"
    		ComVisible="false"
    		CLSCompliant="true"
    		Guid="F13786C9-2DCA-4529-8D78-CD1420A3E489"
    		AssemblyVersion="$(year).$(month).$(day).$(time)"
    		AssemblyFileVersion="$(year).$(month).$(day).$(time)"/>
    </Target>

This translates to:

     <Target Name="Version">

Create a new target called version.



    <Time Format="yyyy">
        <Output TaskParameter="FormattedTime" PropertyName="year" />
    </Time>

Create a new build property called year that is the current year in the form `yyyy`. This is repeated for the month, day and time.

    <Message Text="Version: $(year).$(month).$(day).$(time)" /> 

This is simply for diagnostics purposes and dumps a message out as part of the build process.

    <AssemblyInfo CodeLanguage="CS"
                  OutputFile="Properties\AssemblyInfo.cs"
                  AssemblyTitle="JacsWebsite"
                  AssemblyDescription=""
                  AssemblyCompany="http://www.jacquelinewhite.co.uk/"
                  AssemblyProduct="JacquelineWhite.co.uk"
                  AssemblyCopyright="Copyright &copy; Jacqueline White 2011"
                  ComVisible="false"
                  CLSCompliant="true"
                  Guid="F13786C9-2DCA-4529-8D78-CD1420A3E489"
                  AssemblyVersion="$(year).$(month).$(day).$(time)"
                  AssemblyFileVersion="$(year).$(month).$(day).$(time)" />


Then we wire up our new assembly info file, making sure we fill in everything that we need. At this point we are able to set the `AssemblyVersion` and the `AssemblyFileVersion` with our new timestamp.

The last thing we must do is to let MSBuild know when to run this task. I opted to put this in as part of the “BeforeBuild” target. If you've got other targets running then you may have a better place to put it, but this works fine for me. The key part is to make sure this task runs before the compiler runs.

    <Target Name="BeforeBuild" DependsOnTargets="Version">
    </Target>

That's it, my assembly now has a nice timestamp, which makes it pretty simple to track when it was built.
