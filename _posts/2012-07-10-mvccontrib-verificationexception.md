---
layout: post
category : mvc
tags : [mvc]
title : "MvcContrib VerificationException with MVC 4"
---

Recently had this issue with MvcContrib and MVC 4 which was a pain to track down. When unit testing a controller that made use of MvcContrib it threw a VerificationException.

{% highlight csharp %}
System.Security.VerificationException : Method MvcContrib.ControllerExtensions.RedirectToAction: type argument 'MyApp.Controllers.HomeController' violates the constraint of type parameter 'T'.
{% endhighlight %}

Because I'm using MVC 4 I'm also using MVC Futures 4 (System.Web.Mvc), with MvcContrib referencing an older version A quick assembly binding redirect sorts that out.

{% highlight xml %}
<runtime>
	<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
		<dependentAssembly>
			<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
			<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="4.0.0.0" />
		</dependentAssembly>
	</assemblyBinding>
</runtime>
{% endhighlight %}

Job done.