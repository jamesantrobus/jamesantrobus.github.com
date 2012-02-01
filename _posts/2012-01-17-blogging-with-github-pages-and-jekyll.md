---
layout: post
category : first
tags : [first]
title : Blogging with GitHub Pages and Jekyll
---

I've had a hosted Wordpress blog for a while but never taken the blogging thing too seriously. After finding a bit of motivation recently I began weighing up different blogging options. Wordpress, Thumblr and Posterous are all great but seemed overkill considering what I'll be using it for. I favour simplicity and wanted something flexible that I wouldn't be tied into, but most of all was fun to use.

###Enter GitHub Pages

You're probably already aware of [GitHub Pages][1] so I won't go into the details, but if you're unfamiliar you should check out their docs. In a nutshell you can create a GitHub repository named {username}.github.com and its contents will become available at http://{username}.github.com. Each commit to the repository pushes the contents to the site. The first commit takes about 10 minutes but it's nearly instant for every commit after that.

###Throwing Jekyll into the mix

After a commit GitHub doesn't just do a quick copy & paste to deploy your site. It also runs [Jekyll][2], a "blog-aware static site generator"; Markdowns go in one end - blogs come out the other. 

Jekyll is convention-heavy and knows where to look for your blog posts and includes (partials/user controls). You can get access to posts and other data through [Template Data][3], made available to templates through the Liquid templating system. For example, I list out the posts on my homepage like so:

{% highlight html %}
{{ "{% for post in site.posts" }} %}
	<li>
		<span>{{ "{{ post.date | date_to_string" }} }}</span> 
		<a href="{{ "{{ post.url" }} }}">{{ "{{ post.title" }} }}</a>
	</li>
{{ "{% endfor" }} %}
{% endhighlight %}

When the Jekyll command is ran the \_posts directory gets traversed and parsed to list out the posts in the static site generated.

Admittedly I didn't see the point of Jekyll a while ago. If you're going to deal with posts, categories and tags it must be better to use a database rather than plain-text files, right? It certainly won't be for everyone but I love the simplicity.

###Wrapping up

I haven't covered much but there are plenty of resources around to get started. I cloned [Jekyll Bootstrap][4] initially and worked from there. It'll be interesting to see how I get along with Jekyll in the coming months but for now it does just what I need without the fluff.

[1]: http://pages.github.com/
[2]: https://github.com/mojombo/jekyll
[3]: https://github.com/mojombo/jekyll/wiki/template-data
[4]: https://github.com/plusjade/jekyll-bootstrap