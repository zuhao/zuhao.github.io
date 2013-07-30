---
layout: post
title: "How to Draw an Axis Using Plotrb"
category: gsoc2013
---

Acknowledgment: This tutorial is based on Vega's [documentation](https://github.com/trifacta/vega/wiki/Axes).
___

Starting from this post, as I'm gradually rolling out the DSL, I'm going to write a series of tutorials of how to use Plotrb. Just a reminder, these tutorials will likely to be updated frequently, due to the fact that the DSL may change from time to time, until the first stable version of Plotrb is released. Any feedback on the grammar of the DSL is especially welcomed.

Today, it is about drawing the axes using Plotrb. 

Vega, the underlying library for Plortb, only supports axes for Cartesian coordinates currently. Support for polar coordinates may be introduced in future versions.

Each subsection below describes an attribute of the Axis. There are three different ways to set the attributes.

_(All methods are chainable, except those end with a `?`, which are used to get the boolean value of the attribute. For example, `grid?`, `above?`, or `below?`.)_

1. When initializing a new instance of Axis.

	```ruby
	axis = Plotrb::Axis.new(foo: 'some_value', bar: 'some_other_value')
	```

2. When calling the (chainable) method on an Axis instance

	```ruby
	axis = Plotrb::Axis.new.foo('some_value').bar('some_other_value')
	```

3. When inside a block

	```ruby
	axis = Plotrb::Axis.new do
	  foo 'some_value'
	  bar 'some_other_value'
	end
	```

These are standard ways of setting attributes, thus I'm going to omit them below. Instead, I will introduce some grammar (syntactic sugar, really) for each attribute. They are _recommended_ ways of using Plotrb. 

### type

There are only two types of axis, namely `x` or `y`. You can also specify the type of the axis directly inside a visualization.

```ruby
vis = Plotrb::Visualization.new
axis = vis.x_axis
```

### scale

Each axis is backed up by a scale previously defined in the visualization. You can call ``from`, instead of `scale`, to specify the underlying Scale for the axis.

```ruby
axis.from('some_scale_name')
```

You may either pass in the scale name, or the scale instance itself.

```ruby
my_scale = Plotrb::Scale.new
axis.from(my_scale)
```

### offset

You can also call `offset_by` if that reads more natural to you.

```ruby
axis.type(:x) do
  offset_by 5
end
```

### orient

The axis can be placed at four different orientations, namely `top`, `bottom`, `left`, and `right`. You may simply set the orientation as follows,

```ruby
x_axis.at_bottom
y_axis.at_right
```

### layer

Similar to orient, an axis can also be placed above or below the data marks. You may set the layer as follows,

```ruby
x_axis.in_front
y_axis.in_back
# or 
x_axis.above
y_axis.below
```

### title

You can also provide the offset when setting the title for the axis.

```ruby
axis.title('some_title', 5)
```

### title_offset

You can set the offset for the title separately.

### format

For more about the formatting pattern for the axis labels, please refer to [D3's format patten](https://github.com/mbostock/d3/wiki/Formatting).

### ticks

You may also specify the number of ticks on the axis as follows,

```ruby
axis.type(:x) do
  with_20_ticks
end
```

### subdivide

You may also specify the number of minor ticks between major ticks as follows,

```ruby
axis.with_20_ticks do
  subdivide_by 5
end
```

### tick_padding

You can set the padding between ticks and text labels.

### tick_size

You can set the size of major, minor, and end ticks.

### tick_size_major

You can also try the following if it reads more natural.

```ruby
axis.type(:x) do
  major_tick_size 10
end
```

### tick_size_minor

See above.

### tick_size_end

See above. Set the size of the end ticks.

### grid

You can also use `show_grid` or `with_grid` to specify that gridlines should be created in addition to ticks.























