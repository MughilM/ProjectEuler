---
layout: page
title: "All Problems"
---

Below are all of the problems so far, in numerical order, as opposed to date solved order in the Posts section.

<div>
    {% assign numberedPosts = site.posts | sort: "number" %}
    {% for post in numberedPosts %}
    	<a href="{{ post.url | prepend:site.baseurl }}">{{ post.title }}</a>
    	<br>
    {% endfor %}
</div>