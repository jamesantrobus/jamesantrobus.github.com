---
layout: post
category : .net
tags : [.net, nhibernate, orm]
title : "NHibernate Gotchas: All objects in session updated"
---

Lets say we have a POCO called Developer, with ID, Name and Framework properties. With Fluent NHibernate I can map this like so:

{% highlight csharp %}
public class DeveloperMap : ClassMap<Developer>
{
	public DeveloperMap()
	{
		Id(x => x.Id);
		Map(x => x.Name);
		Map(x => x.Framework).CustomType<int>();
	}
}
{% endhighlight %}

Enum is mapped to the database as an int as you'd (might) expect. If we use the PersistenceSpecification testing class provided by Fluent NHibernate we can verify that our mappings save and retrieve our class successfully.

{% highlight csharp %}
[Test]
public void VerifyDeveloperMappings()
{
	new PersistenceSpecification<Developer>(_session)
		.CheckProperty(x => x.Name, "James")
		.CheckProperty(x => x.Framework, WebFramework.Django)
		.VerifyTheMappings();
}
{% endhighlight %}

This test passes, but our updates don't look so good. Running the following and examining the generated SQL with NHProf (or show_sql) is a little surprising.

{% highlight csharp %}
[Test]
public void Gotcha_CastingToIntMakesObjectDirty()
{
	_session.Save(new Developer { Name = "Peter", Framework = WebFramework.MVC });
	_session.Save(new Developer { Name = "Brian", Framework = WebFramework.Rails });
	_session.Save(new Developer { Name = "Meg", Framework = WebFramework.Django });

	_session.Flush();
	_session.Clear();

	var allDevelopers = _session.Query<Developer>().ToList();
	var first = allDevelopers.First();
	first.Name = "James";
	_session.Save(first);

	_session.Flush();
}
{% endhighlight %}

[![All session objects updated](/assets/images/2012-05-02/update-all.png)](/assets/images/2012-05-02/update-all.png)

When NHibernate casts from an int to enum the object is marked as dirty. When the session's flushed all dirty objects are updated, in this case all developer objects in session.

Fortunately the mappings can be updated to map the Framework property without specifying a custom type.

{% highlight csharp %}
Map(x => x.Framework);
{% endhighlight %}

I've started an [NHibernate Gotchas][1] project on GitHub to add a few issues like this to.

[1]: https://github.com/jamesantrobus/nhibernate-gotchas/
