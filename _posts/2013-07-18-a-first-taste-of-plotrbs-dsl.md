---
layout: post
title: "A First Taste of Plotrb's DSL"
category: gsoc2013
---

In the previous [post](http://wanzuhao.com/posts/how-plotrb-works-internally/), I've talked about some of the internal APIs of Plotrb. After it was published, I got some feedback saying it was still a bit verbose at many places. I do agree, but in my defense, the APIs demonstrated are for _internal_ use only, which means the users are not required to know any of it to use Plotrb. 

I've promised a more concise and elegant DSL, and now here it is. (Well, maybe just a part of it.)

Firstly, let's assume we have a top-level visualization instance.

{% highlight ruby %}
vis = ::Plotrb::Visualization.new(name: 'my_vis')
{% endhighlight %}

We want to add scales to this visualization. Vega defines scales as

>(Scales are) functions that transform a domain of data values (numbers, dates, strings, etc) to a range of visual values (pixels, colors, sizes).

The following is a list of sample use-cases for adding a scale to our visualization.

(Just to make it more fun, don't read the comments first and guess what each example does. Trust me, you'll have a pretty good idea just from the code. Yes, the DSL is _that_ awesome!)

{% highlight ruby %}
vis.ordinal_scale.from('data_source.category').to_colors
# transform category field of our data_source into a 10-color categorical palette

vis.linear_scale.from('population.weight').round.to_height
# transform weight field of population data into a range of the current canvas' height
# also round the numbers for better readability

vis.utc_scale.from('events.date').in_weeks.to_width
# transform event dates into a range of the current canvas' width
# also set the time interval to a week and thus change the dates to a more human-friendly range

vis.pow_scale.from('earthquake.magnitude').to_height.in_exponent(10).include_zero
# transform magnitudes into a range of the height of the canvas in exponents, showing the relative strength
# also include zero as the baseline
{% endhighlight %}

It doesn't matter if you switch any of the method calls, as long as they read _naturally_ for you. For instance, you can also write the last example as

{% highlight ruby %}
vis.pow_scale.in_exponent(10).from('earthquake.magitude').include_zero.to_height
{% endhighlight %}

Being simple does not necessarily mean we have to compromise on functionality. You are free to choose the more Rubyish way, or the old-fashioned way (i.e. using the internal APIs). Both can do the exact same things.

For more implementation details, you can take a look at the source file [here](https://github.com/zuhao/plotrb/blob/master/lib/plotrb/scales.rb). Do you like this first taste of Plotrb's grammar? Any feedback is welcomed!
