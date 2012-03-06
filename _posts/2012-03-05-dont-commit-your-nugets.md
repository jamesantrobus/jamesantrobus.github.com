---
layout: post
category : .net
tags : [.net, nuget]
title : Don't commit your NuGets
---

It's widely accepted that external or third-party libraries shouldn't be checked into your project repository. Tools like Bundler for Ruby and Maven for Java manage dependencies and hook up nicely with your CI server of choice.

The lack of dependency management in .NET (until NuGet came along) means that many repos have DLLs for NUnit, NHibernate or whatever external libs you use. NuGet has made getting these libraries so much better but often they are still committed into the repo.

###NuGet makes it easy

Fortunately NuGet is able to fetch dependencies when a Solution is built.

[![Enable Package Restore](/assets/images/2012-03-05/1-enable-package-restore.png)](/assets/images/2012-03-05/1-enable-package-restore.png)
> Select 'Enable NuGet Package Restore' from the Solution context menu

[![2 Continue](/assets/images/2012-03-05/2-continue.png)](/assets/images/2012-03-05/2-continue.png)
> Hit yes on the next popup to configure the solution

[![3 Configuring](/assets/images/2012-03-05/3-configuring.png)](/assets/images/2012-03-05/3-configuring.png)
> Wait...

[![4 Complete With Targets](/assets/images/2012-03-05/4-complete-with-targets.png)](/assets/images/2012-03-05/4-complete-with-targets.png)
> Done!

A .nuget directory is created at the solution level and included in your project. If you unload your project and open the .csproj for edit in VS (or just open in a text editor) you can see the MSBuild target that's been added for restoring NuGet packages.

[![5 Targets](/assets/images/2012-03-05/5-targets.png)](/assets/images/2012-03-05/5-targets.png)
> Reference to .nuget\NuGet.targets

###That's it

Just update your version control to ignore packages (you'll still need packages\repositories.config) and your CI system and co-workers will pull packages down automatically on the next build.

I've got a [demo project][1] on GitHub that's using Package Restore. [This commit][2] shows the modifications made when running Enable NuGet Package Restore.

###External links
* [NuGet docs for Package Restore][3]
* Package Restore was briefly discussed on episode 134 of [Herding Code][4]

###Comments

Haven't really thought out comments for this blog yet - drop me a [Tweet][5] if you have a comment.

[1]: https://github.com/jamesantrobus/missing-nugets/
[2]: https://github.com/jamesantrobus/missing-nugets/commit/dcb258d8fd29300df4d1de42ab8184ec5e073bf7
[3]: http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages
[4]: http://herdingcode.com/?p=396
[5]: http://twitter.com/james_antrobus/