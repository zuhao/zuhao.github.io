---
layout: post
title: "How to Transform Data in Plotrb"
category: gsoc2013
---

Acknowledgment: This tutorial is based on Vega's [documentation](https://github.com/trifacta/vega/wiki/Data-Transforms).
___

Data Transform is one of the most important components of Plotrb. It is responsible for performing operations on data set prior to visualization.

A transform is specified by its type. In this article I will describe all transform types allowed by Vega. 

First of all, to create a new Transform instance, you can either

```ruby
t = Plotrb::Transform.new(:filter) # filter is one of the allowed types
```

Or directly call

```ruby
t = Plotrb::Transform.filter
```

Differernt types will have different attributes to further specify the transforms.

## Data Manipulation Transforms

### array

Maps a data object to an array of selected values referenced by `fields`.

```ruby
t = Plotrb::Transform.array.fields('pop.weight', 'pop.height')
```

You can also use `take` to specify the field references.

```ruby
t = Plotrb::Transform.array do
  take 'pop.weight', 'pop.height'
end
```

### copy

Copys values into a top-level data object.

* `from` specifies the name of the object to copy values from
* `fields` specifies the fields to copy
* `as` (optional) specifies the new name of the copied fields. This must be of the same size as `fields`

You can also use `take` to replace `fields` if it reads more natural.

```ruby
t = Plotrb::Transform.copy do
  from 'population'
  take 'weight', 'height'
  as 'w', 'h'
end
```

### cross

Computes the cross-product of two data sets.

* `with` specifies the secondary data set
* `include_diagonal` or `diagonal` specifies if items along the diagonal of the cross-product will be included in the output

```ruby
t = Plotrb::Transform.cross.with('another_data').include_diagonal
```

If you don't supply the secondary data set, the cross-product will be against the data set itself.

For example, if data is [1, 2, 3], `cross.include_diagonal` will produce

```json
[
  {"a":1, "b":1},
  {"a":1, "b":2},
  {"a":1, "b":3},
  {"a":2, "b":1},
  {"a":2, "b":2},
  {"a":2, "b":3},
  {"a":3, "b":1},
  {"a":3, "b":2},
  {"a":3, "b":3},
]
```

### facet

Organizes data into groups.

* `keys` specifies the fields to use as keys
* `sort` (optional) specifies sorting criteria for each facet

This is similar to `group by` operation in SQL, so you may replace `keys` with `group_by`.

```ruby
t = Plotrb::Transform.facet.group_by('category')
```

For more details of how the output is organized, please refer to Vega's wiki page [here](https://github.com/trifacta/vega/wiki/Data-Transforms#-facet).

### filter

Filter the data set to remove unwanted items according to `test` expression.

```ruby
t = Plotrb::Transform.filter.test('d.data.x > 10')
```

### flatten

Converts a faceted or hierarchical data set back into a flat, tabular structure.

```ruby
t = Plotrb::Transform.flatten
```

### fold

Collapses one or more data properties referenced by `fields` into two: a key (containing the original data property name) and a value (containing the data value).

You can also use `into` to replace `fields`.

```ruby
t = Plotrb::Transform.fold.into('data.gold', 'data.silver')
```

For the following input

```json
[
  {"data": {"country": "USA", "gold":10, "silver":20}}, 
  {"data": {"country": "Canada", "gold":7, "silver":26}}
]
```

The output will be

```json
[
  {"index": 0, "key":"data.gold", "value":10, "data": {"country": "USA"}},
  {"index": 1, "key":"data.silver", "value":20, "data": {"country": "USA"}},
  {"index": 2, "key":"data.gold", "value":7, "data": {"country": "Canada"}},
  {"index": 3, "key":"data.silver", "value":26, "data": {"country": "Canada"}}
]
```

This can be used to transform matrix data into standardized format.

### formula

Applies formula to the data set, and stores the result in a new field.

* `field` specifies the new field to store the results. You can also use `into`
* `expr` specifies the expression for the formula. You can also use `apply`

```ruby
t = Plotrb::Transform.formula.apply('abs(d.data.x * d.data.y)').into('xy')
```

### slice

Generates a subset of the data array.

* `by` specifies how to slice the array. There are three special options `:min`, `:max`, and `:median` to extract the single element.
* `field` specifies the field to store the extracted element if `by` attribute is one of the three special options.

Assume the data is `[5, 6, 7, 8, 9, 10, 11]`.

```ruby
t = Plotrb::Transform.slice

t.by(3) # => [8, 9, 10, 11]
t.by(-2) # => [10, 11]
t.by([2, 5]) #=> [7, 8, 9]
t.by(:min).field('min_value')
```

### sort

Sorts the values by fields as criteria. You can either use `#reverse` or prefixing a "-" character in front of the fields to specify descending order.

```ruby
t = Plotrb::Transform.sort.by('foo')
t = Plotrb::Transform.sort.by('bar').reverse
t = Plotrb::Transform.sort.by('-baz')
```

### stats

Computes statistics for the data set. They are count, minimum, maximum, sum, mean, sample variance, and sample standard deviation.

* `value` or `from` specifies the field to compute the statistics
* `median` or `include_median` specifies whether median should be computed
* `assign` or `store_stats` specifies whether a `stat` property will be added to each data element

```ruby
t = Plotrb::Transform.stats do
  from 'data.foo'
  include_median
  store_stats
end
```

### truncate

Truncates a string into specified length.

* `value` or `from` specifies the field containing values to truncate
* `output` or `to` specifies the field to store the results
* `limit` or `max_length` specifies the max length of the truncated string
* `position` specifies the position from which to remove the text. It must be one of `:left`, `:middle`, or `:right`.
* `ellipsis` specifies the text to use as an ellipsis for truncated text. The default is "...".
* 'wordbreak' specifies whether to truncate along word boundaries

```ruby
t = Plotrb::Transform.truncate do
  from 'data.text'
  to 'truncated'
  max_length 20
  position :middle
  ellipsis '***'
  wordbreak
end
```

### unique

Construct a new data set that contains unique values for the specified field.

* `field` or `from` specifies the data field for which to compute unique values
* `as` or `to` specifies the name of the new data set

```ruby
t = Plotrb::Transform.unique.from('data.foo').to('new_data')
```

### window

Performs a "sliding window" over a data array and outputs each window frame.

* `size` specifies the number of elements of the sliding window
* `step` specifies the step size by which to advance the window per frame

```ruby
t = Plotrb::Transform.window.size(3).step(2)
```

The above example returns triples in the data set, such that the last value of the previous triple is the first value in the next triple, because the `step` size is 2.

### zip

Merges two data sets together according to a join key. If no key is provided, the data sets are merged by indices.

* `with` specifies the secondary data set to zip with
* `as` specifies the name of the field to store the secondary data set values
* `key` or `match` the field in the primary data set to match against the secondary data set
* `with_key` or `against` the field in the secondary data set to match against the primary data set
* `default` specifies the default value to use if no matching key value is found

```ruby
t = Plotrb::Transform.zip do
  with 'unemployment'
  match 'data.id'
  against 'data.key'
  as 'value'
end
```

This example matches records in the input data with records in the secondary data set named "unemployment", where the values of data.id (primary data) and data.key (secondary data) match. Matching values in the secondary data are added to the primary data in the field named "value".


## Visual Encoding Transforms

### force

Performs force-directed layout for network data. 

The tranform acts on two data sets: one containing nodes and one containing links. Apply the transform to the node data, and include the name of the link data as a transform parameter.

* `links` specifies the name of the link data set. Object in the data set must have `source` and `target` properties.
* `size` specifies the dimensions of the force layout
* `iterations` specifies the number of iterations to run the force directed layout
* `charge` specifies the strength of the charge each node exerts. It can be either a number for all nodes, or a reference to a data field.
* `link_distance` specifies the length of edges. It can be either a number for all edges, or a reference to a data field.
* `link_strength` specifies the tension of the edges (the spring constant). It can be either a number for all edges, or a reference to a data field.
* `friction` specifies the strength of the friction force used to stabilize the layout
* `theta` specifies the theta parameter for the Barnes-Hut algorithm used to compute charge forces between nodes
* `gravity` specifies the strength of the pseudo-gravity force that pulls nodes towards the center of the layout area
* `alpha` specifies how much node positions are adjusted at each step

### geo

Performs a cartographic projection. Given longitude and latitude values, sets corresponding x and y properties for a mark.

* `projection` specifies the type of cartographic project to use. The supported types are `:albers`, `:albers_usa`, ':azimuthal_equal_area', `:azimuthal_equidistant`, `:conic_conformal`, `:conic_equal_area`, `:conic_equidistant`, `:equirectangular`, `:gnomonic`, `:mercator` (default), `:orthographic`, `:stereographic`, and `:transverse_mercator`, as defined by [D3](https://github.com/mbostock/d3/wiki/Geo-Projections#standard-projections).
* `lon` specifies the longitude values
* `lat` specifies the latitude values
* `center` specifies the center of the projection
* `translate` specifies the translation of the projection
* `scale` specifies the scale of the projection
* `rotate` specifies the rotation of the projection
* `precision` specifies the desired precision of the projection
* `clip_angle` specifies the clip angle of the projection

### geopath

Creates paths for geographic regions such as countries, states and counties.

* `value` specifies the data field containing GeoJSON feature data
* `projection` see #geo
* `center` see #geo
* `translate` see #geo
* `scale` see #geo
* `rotate` see #geo
* `precision` see #geo
* `clip_angle` see #geo

### link

Computes a path definition for connecting nodes within a network or tree diagram.

* `source` specifies the data field containing the source node for the link
* `target` specifies the data field containing the target node for the link
* `shape` specifies the path shape to use. It must be one of `:line`, `:curve`, `:diagonal`, `:diagonal_x`, or `:diagonal_y`.
* `tension` specifies the tension parameter in the range `[0, 1]` for the "tightness" of curve-shaped links.

### pie

Computes a pie chart layout.

* `sort` specifies whether to sort the data prior to computing angles
* `value` specifies the data values to encode as angular spans.

If `value` property is not given, all pie slices will have equal spans.

### stack

Computes layout values for stacked graphs.

* `point` specifies the data field determining the points at which to stack. For example when values are stacked vertically, this corresponds to the x-coordinates.
* `height` specifies the data field determining the height of a stack
* `offset` specifies the baseline offset style. It must be one of `:zero`, `:silhouette`, `:wiggle`, or `:expand`.
* `order` specifies the sort order the stack layers. It must be one of `default`, `reverse`, or `inside-out`.

The `:silhouette` offset will center the stacks, while `:wiggle` will attempt to minimize changes in slope to make the graph easier to read. If `:expand` is chosen, the output values will be in the range [0,1].

You can also call `#reverse` or `#inside_out` directly to set the order.

### treemap

Computes a squarified treemap layout.

* `padding` specifies the the padding around internal nodes in the treemap
* `ratio` specifies the target aspect ration for the layout to optimize. Defaults to golden ration.
* `round` specifies whether treemap cell dimensions will be rounded to integer pixels
* `size` specifies the dimensions of the treemap layout
* `sticky` specifies whether repeated runs of the treemap will use cached partition boundaries, resulting in smoother transition animations, at the cost of unoptimized aspect ratios.
* `value` specifies the data field to determine the area of each leaf-level cell

### wordcloud

Computes a word cloud layout.

* `font` specifies the font face to use
* `font_size` specifies the font size for a word
* `font_style` specifies the font style to use
* `font_weight` specifies the font weight to use
* `padding` specifies the padding around text in the word cloud
* `rotate` specifies the roration angle for a word. The input value can either be a data field reference, or an object describing an assignment policy. For example, the object {"random": [0, 30, 60]} will randomly assign one of 0, 30 or 60 degrees, while {"alternate": [0, 30, 60]} will repeatedly assign the angles 0, 30 and 60 degrees in order.
* `size` specifies teh dimensions of the wordcloud layout
* `text` specifies the data field containing the text to visualize
