---
layout: post
category : .net
tags : [.net, nhibernate, orm]
title : "NHibernate Gotchas: PersistenceSpecification false positives"
---

Fluent NHibernate provides a nifty PersistenceSpecification class for verifying a class has been mapped correctly. Here's an example:

{% highlight csharp %}
[Test]
[TestCase(false, Description = "False positive")]
[TestCase(true, Description = "Failing test (correct)")]
public void Verify_IsActive_Property_Without_Mapping_The_Column(bool isActive)
{
	new PersistenceSpecification<Developer>(Session)
		.CheckProperty(x => x.Name, "James")
		.CheckProperty(x => x.Framework, WebFramework.Django)
		.CheckProperty(x => x.IsActive, isActive)
		.VerifyTheMappings();
}
{% endhighlight %}

Taking a look at the code for [VerifyTheMappings()][1] we can see that it will:

* Instantiate a new Developer and set the 3 properties
* Save to session, then flush and clear
* Grab a fresh instance of the Developer from session
* Compare the properties one-by-one

Knowing this, we can make the test above pass without even mapping IsActive. VerifyTheMappings will persist the Developer object without the unmapped IsActive property. It will then instantiate a new Developer object where IsActive is false by default, before comparing them and passing the test.

Likewise if we set IsActive to true in the parameterless constructor we can also make the test pass using true as the test value. Boolean is obviously the easiest one to spot, enums (where the first item has a value of 0) are slightly harder.

[1]: https://github.com/jagregory/fluent-nhibernate/blob/4900f065537142e6f36e6520840195a65ef8d10f/src/FluentNHibernate/Testing/PersistenceSpecification.cs#L43-L67
