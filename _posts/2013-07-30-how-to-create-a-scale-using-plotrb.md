---
layout: post
title: "How to Create a Scale Using Plotrb"
category: gsoc2013
---

Acknowledgment: This tutorial is based on Vega's [documentation](https://github.com/trifacta/vega/wiki/Scales).
___

This is the second post of the tutorial series. Today I'm going to introduce how to create a Scale using Plotrb.

>(Scales are) functions that transform a domain of data values (numbers, dates, strings, etc) to a range of visual values (pixels, colors, sizes).

As mentioned in the previous [article](../how-to-draw-an-axis-using-plotrb), all the attributes of a Scale listed below can be set in three standard ways. The following is some recommended grammar that would hopefully make the code shorter and more readable.

### name

Set the _unique_ name of the scale.

### type

You can directly set the type when creating a new scale in a visualization. The valid types are `linear`, `log`, `pow`, `sqrt`, `quantile`, `quantize`, `threshold`, `time`, `utc`, and `ordinal`.

```ruby
vis = Plotrb::Visualization.new
scale = vis.linear_scale
```

### domain

You can also use `from` to set the domain of the scale. A domain is a reference which specifies the name of data source and the field.

```ruby
scale.from('some_data_source.some_field')
```

By default, if the data field is not provided, Plotrb will assume that the field is the implicit index of the data. Of course, you may explicitly specify to use index.

```ruby
scale.from('some_data_source.index')
```

It's also possible to set the domain directly without reference to any other data sources. For quantitative data, you can pass in an array of two numbers representing the minimum and maximum values.

```ruby
scale.from([1, 100])
```

For ordinal/categorical data, you simply list all of them in an array.

```ruby
scale.from(%w(Mon Tue Wed Thu Fri Sat Sun))
```

In addition, you can pass in two more arguments, specifying the minimum and maximum values of the domain.

```ruby
scale.from('some_population.ages', 0, 150)
```

### domain_min

Set the minimum value of the domain. It can be a single value, or a data reference similar to the one used for domain.

### domain_max

Set the maximum value of the domain. See `domain_min`.

### range

The range is a set of visual values. You can use `to` to set the range of the scale. Similar to domain, you can pass in an array of two numbers representing the minimum and the maximum for numerical range, or all values for ordinal data.

```ruby
scale.from('some_data.some_field').to([0, 20])
scale.from([1, 7]).to(%w(Mon Tue Wed Thu Fri Sat Sun))
```

There are more goodies for ordinal data. The following is a list of pre-defined range values that often come very handy.

```ruby
# set the range to [0, width of the data rectangle]
scale.to_width

# set the range to [0, height of the data rectangle]
scale.to_height

# set the range to 6 different shapes,
# namely circle, cross, diamond, square, triangle-down, and triangle-up
scale.to_shapes

# set the range to a 10-color categorical palette
scale.to_colors

# set the range to a 20-color categorical palette
scale.to_more_colors
```

### range_min

See `domain_min`.

### range_max

See `domain_max`.

### reverse

Specify if flips the scale range.

### round

Specify if rounds numerical range to integers.

### points

You can use `as_points` or `as_bands` to specify whether to distribute the ordinal values at uniformly spaced points or evenly spaced bands.

### padding

Set the spacing among ordinal elements in the range. For more information please refer to Vega's [wiki](https://github.com/trifacta/vega/wiki/Scales#ordinal-scale-properties) and D3's [documentation](https://github.com/mbostock/d3/wiki/Ordinal-Scales).

### sort

Specify if the scale domain will be sorted according to natural order.

### exponent

Only for `pow` type of scale, set the exponent of the transformation.

### nice

You can use `nicely` for quantitative scales to modifies the domain to a more human-friendly number range.

```ruby
scale.from('some_data.decimal_numbers').to([0, 100]).nicely
```

For time scales (`time` or `utc` types), you can specify the time interval to a more human-friendly range as follows,

```ruby
scale.from('some_data.some_time').in_seconds
```

Available options are `in_seconds`, `in_minutes`, `in_hours`, `in_days`, `in_weeks`, `in_months`, and `in_years`.

### zero

You can use `include_zero` to specify if zero baseline is included in the domain (for quantitative scale only).

### clamp

Specify if exceeded domain values are clamped to either the minimum or maximum.
