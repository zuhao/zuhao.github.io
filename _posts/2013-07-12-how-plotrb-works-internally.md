---
layout: post
title: "How Plotrb Works Internally"
category: gsoc2013
---

This week I have wrapped up a bare minimum working version of Plotrb. In this article I am going to contruct the first demo using Plotrb's internal APIs only. A plotting DSL still needs to be designed and implemented on top of the APIs, and that will be the job for the next several weeks. Despite being a bit long (but surely shorter than the equivalant if we were to write in D3), the demo is also a great way to show how Plotrb works internally.

There are a few important components that we can find in most of the plots, namely the data, the scales, the axes, and the marks. So we're gonna construct them one at a time.

First of all, data is the very soul of the plot. The reason we use plots is primarily to visualize _data_. The data for plotrb can come in different forms, such as json, csv, tsv, or internal objects such as hashes and arrays. In this example, we will read josn data from a url, and it contains coordinates of a lot of points to be plotted. We can have multiple data sets; just put them together in an array for later use.

{% highlight ruby %}
# Data
json_url = 'http://trifacta.github.io/vega/editor/data/points.json'
data = [
  ::Plotrb::Data.new(name: 'points', url: json_url)
  # we can have as many data sets as we like
]
{% endhighlight %}

Next, we need scales that transform our raw _data_ values (domain) into _visual_ values (range). We  reference the data set `points` defined previously as domains. `nice` rounds the values so they are more human-friendly. `range` simply takes the width and height of the canvas. Similarly to data, we group the two scales together.

{% highlight ruby %}
scale_x = ::Plotrb::Scale.new(
  name: 'x', nice: true, range: :width, 
  domain: ::Plotrb::DataRef.new(data: 'points', field: 'data.x')
)
scale_y = ::Plotrb::Scale.new(
  name: 'y', nice: true, range: :height, 
  domain: ::Plotrb::DataRef.new(data: 'points', field: 'data.y')
)
scales = [scale_x, scale_y]
{% endhighlight %}

We can then move on to axes based on the scales defined.

{% highlight ruby %}
# Axes
axis_x = ::Plotrb::Axis.new(type: :x, scale: 'x')
axis_y = ::Plotrb::Axis.new(type: :y, scale: 'y')
axes = [axis_x, axis_y]
{% endhighlight %}

Now the most complex part. Marks. We will use circles by default for `symbol` type; of course we can have other shapes such as square, diamond, etc. There are some primary property sets such as `enter`, `exit`, `update` and `hover`; it's not hard to guess what they does.

We want our circles to be in blue, and transparent inside. Furthermore, when displaying the plot in a web browser, we would like some interactivity, for example enlarging the circle and changing it to pink when the mouse hovers over it.

{% highlight ruby %}
# Marks
mark = ::Plotrb::Mark.new(type: :symbol, from: {data: 'points'})
properties = {
  enter: {
    x: ::Plotrb::ValueRef.new(scale: 'x', field: 'data.x'),
    y: ::Plotrb::ValueRef.new(scale: 'y', field: 'data.y'),
    stroke: ::Plotrb::ValueRef.new(value: :steelblue),
    fill_opacity: ::Plotrb::ValueRef.new(value: 0.5)
  },
  update: {
    fill: ::Plotrb::ValueRef.new(value: :transparent),
    size: ::Plotrb::ValueRef.new(value: 100)
  },
  hover: {
    fill: ::Plotrb::ValueRef.new(value: :pink),
    size: ::Plotrb::ValueRef.new(value: 300)
  }
}
mark.properties = properties
marks = [mark]
{% endhighlight %}

And we are almost done. We have all the components of a plot we need, what's left to do is pull out a paper/canvas/rectangle (whatever you'd like to call it). It is called `Visualization` in Plotrb, and it's the top-level container for all other visual elements we've built previously.

{% highlight ruby %}
# Visualization
padding = {top: 50, left: 50, bottom: 50, right: 50}
visualization = ::Plotrb::Visualization.new(
  name: 'vis', width: 400, height:400, padding: padding, 
  data: data, scales: scales, marks: marks, axes: axes
)
{% endhighlight %}

Now is when the magic happens.

{% highlight ruby %}
visualization.generate_spec(:pretty)
{% endhighlight %}

Wait for it... and Voilà, the JSON specification of the plot!

{% highlight json %}
{
  "name": "vis",
  "width": 400,
  "height": 400,
  "padding": {
    "top": 50,
    "left": 50,
    "bottom": 50,
    "right": 50
  },
  "data": [
    {
      "name": "points",
      "url": "http://trifacta.github.io/vega/editor/data/points.json"
    }
  ],
  "scales": [
    {
      "name": "x",
      "domain": {
        "data": "points",
        "field": "data.x"
      },
      "range": "width",
      "nice": true
    },
    {
      "name": "y",
      "domain": {
        "data": "points",
        "field": "data.y"
      },
      "range": "height",
      "nice": true
    }
  ],
  "marks": [
    {
      "type": "symbol",
      "from": {
        "data": "points"
      },
      "properties": {
        "enter": {
          "x": {
            "field": "data.x",
            "scale": "x"
          },
          "y": {
            "field": "data.y",
            "scale": "y"
          },
          "stroke": {
            "value": "steelblue"
          },
          "fill_opacity": {
            "value": 0.5
          }
        },
        "update": {
          "fill": {
            "value": "transparent"
          },
          "size": {
            "value": 100
          }
        },
        "hover": {
          "fill": {
            "value": "pink"
          },
          "size": {
            "value": 300
          }
        }
      },
      "shape": "cross"
    }
  ],
  "axes": [
    {
      "type": "x",
      "scale": "x"
    },
    {
      "type": "y",
      "scale": "y"
    }
  ]
}
{% endhighlight %}

We can embed it in web pages, or generate png/svg from it directly using Vega's headless mode. For simplicity, we just run `vg2png demo.json demo.png`, and here's what we've got.

![Demo]({{ site.url }}/images/demo.png)

Pretty neat, huh?
