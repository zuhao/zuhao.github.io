---
layout: post
title: "The Start of GSOC 2013"
category: posts
---

This is my second time doing [Google Summer of Code](http://www.google-melange.com/gsoc/homepage/google/gsoc2013). This summer I will be working with the [Ruby Science Foundation](http://sciruby.com), on the project of creating a plotting library based on [D3.js](http://d3js.org), a very powerful javascript library for data-driven visualization. In the recent years, majority of the attention of Ruby community has been placed on web development, thanks to excellent frameworks such as Rails. However, comparing to Python, Ruby still has a long way to catch up in terms of scientific applications. Tools like [matplotlib](http://matplotlib.org) are missing here; hence, this project is an attempt to create a new plotting solution in Ruby, and lay a solid foundation for not only static plotting but also interactive visualization, leveraging the strengths of the underlying D3.

The past two weeks' community bonding period was very different from the last time. So here is a summary of what happened.

I started communicating with the mentors back in April, even before students application started. After some time of discussing and debating, we agreed that the project shouldn't be an re-implementation of D3's functionality in Ruby. Instead, it should build on top of D3, maybe in the form of a wrapper.

The first approach was to create a Ruby DSL whose syntax resembles D3's. We would have to use libraries such as [Opal](http://opalrb.org) to compile arbitrary Ruby methods into javascript functions. Despite it being far from perfect, this is the approach I chose to take. I even created a [demo](https://github.com/zuhao/d3rb) for that. It's worth to mention that during the process, the mentors have offered great guidance for me to keep improving the application.

Shortly after the community bonding period started, I received a thought-provoking [feedback](https://github.com/zuhao/d3rb/issues/1) from another mentor. This led to a new round of [discussion](https://groups.google.com/forum/?fromgroups#!topic/sciruby-dev/n9AkRDZM2no) (and debate of course) in the mailing list.

Long story short, most of us realized the tool bears little value if it were to simply mimic the syntax of D3. In that case, I would feel reluctant to eat my own dog food because after all, if eventually I manage to learn the syntax of the DSL, why don't I just write it in plain ol' javascript directly, and save the hassle of compiling Ruby code to js? Being very flexible and customizable, D3 has drawbacks such as verbosity and complexity for plotting simple/standard charts. Therefore, I am going to create a plotting library with a higher level of abstraction, and thus simpler syntax, built on top of D3. (Future posts will have more details on the design and other things.) It focuses on static chart plotting first, and hopefully interactivity will come later. To make this summer fruitful, I definitely prefer developing some tool people would actually use and be happy about, even if it might not cover 100% use cases. This is the new project [__plotrb__](https://github.com/zuhao/plotrb).

It is very fortunate for me to be in this kind of community, where everyone contributes one way or another, and people are quite open to new ideas. I guess very few could benefit as much as we did from the bonding period. A promising start.