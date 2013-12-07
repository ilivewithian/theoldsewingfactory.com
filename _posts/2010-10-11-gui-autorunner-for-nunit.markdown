---
layout: post
title: GUI Autorunner for NUnit
---

<a href="/images/WorksOnMyMachine.png"><img class="alignright size-full wp-image-130" title="it works on my machine" src="/images/WorksOnMyMachine.png" alt="" width="200" height="193" /></a>For a sometime MBUnit has had an auto runner, sadly NUnit has managed to avoid getting one for itself. I like using NUnit, in fact all my unit tests are now written in NUnit. I really like the idea of having an auto runner, so I built one.
The binaries are here: [Auto Runner (Binaries)](http://theoldsewingfactory.com/wp-content/uploads/2010/10/Auto-Runner-Binaries.zip) and the source is here: [Autorunner NUnit-2.5.7.10213 (src)](http://theoldsewingfactory.com/wp-content/uploads/2010/10/Autorunner-NUnit-2.5.7.10213-src.zip) It's not had much testing, I simply got it to work for the tests that I was trying to run and not a lot more in fact I think I may use it to get my "[Works on My Machine](http://www.codinghorror.com/blog/2007/03/the-works-on-my-machine-certification-program.html)" certification.

Using it is fairly simple, open up the GUI, load your project as normal and in the test menu there is a new option called "Auto Run" clicking that kicks off the first run and then waits for changes to the Assemblies, when they are changed a new test run is kicked off. To stop the auto run simply click the same menu item.
![Auto Runner Grab](http://theoldsewingfactory.com/wp-content/uploads/2010/10/Auto-Runner-Grab-300x192.png "Auto Runner Grab")
If people like this then I'll take a look at pushing it into the main NUnit project. What do you think?