---
layout: post
title: "How to Draw Marks in Plotrb"
category: gsoc2013
---

Acknowledgment: This tutorial is based on Vega's [documentation](https://github.com/trifacta/vega/wiki/Marks).
___

Marks are the basic visual building blocks of a visualization; they provide basic shapes whose properties can be set according to the backing data.

The available types of mark defined by Vega are `rect`, `symbol`, `path`, `arc`, `area`, `line`, `image`, and `text`.

In order to create a new Mark object, you may choose either of the following. You will have to specify the type, and it can't be changed after the object has been created.

```ruby
mark = Plotrb::Mark.new(:rect)
```

or,

```ruby
mark = Plotrb::Mark.rect
```

## Top-Level Mark Properties

The following properties are applicable to all mark types.

### name

Create a unique name for the mark object (optional).

```ruby
mark = Plotrb::Mark.rect.name('my_mark')
```

### description

Create a description for this mark (optional).

```ruby
mark.description('this is a rect mark')
```

### from_data

Reference (by name) to the Data this mark set should visualize.

```ruby
mark.from_data('my_data')
```

### with_transform

Provide an array of Transforms to apply on the data.

```ruby
t = Plotrb::Transform.facet.group_by('category')
mark.from_data('my_data').with_transform(t)
```

### key

A data field to use as a unique key for data binding. When a visualization's data is updated, the key value will be used to match data elements to existing mark instances. It's important to use a key field to enable object constancy for transitions over dynamic data.

If no key is provided, the index in the data array will be used.

### delay

Specify the transition delay for mark updates in milliseconds.

### ease

Specify the transition easing function for mark updates. The supported easing types are `linear`, `quad`, `cubic`, `sin`, `exp`, `circle`, and `bounce`, plus the modifiers `in`, `out`, `in-out`, and `out-in`. Please refer to [D3 ease function documentation](https://github.com/mbostock/d3/wiki/Transitions#wiki-d3_ease) for more details

### properties

There are four sets of properties for a mark instance, namely `enter`, `update`, `exit`, and `hover`. For each set, you can specify the following attributes.

## Shared Visual Properties

### x

Specify the first (typically left-most) x-coordinate. You can also use `x_start` or `left`.

### x2

Specify the second (typically right-most) x-coordinate. You can also use `x_end` or `right`.

### width

Specify the width of the mark.

### y

Specify the first (typically top-most) y-coordinate. You can also use `y_start` or `top`.

### y2

Specify the second (typically bottom-most) y-coordinate. You can also use `y_end` or `bottom`.

### height

Specify the height of the mark.

### opacity

Specify the overall opacity

### fill

Specify the fill color.

### fill_opacity

Specify the fill opacity.

### stroke

Specify the stroke color.

### stroke_width

Specify the stroke width in pixels.

### stroke_opacity

Specify the stroke opacity.

### stroke_dash

An array of alternating stroke spec lengths for creating dashed or dotted lines.

### stroke_dash_offset

Specify the offset into which to begin drawing with the stroke dash array.

## Type-Specific Attributes

The following attributes are defined for their specific types only.

### rect

no additional attributes.

### symbol

#### size

Specify the pixel area of the symbol.

#### shape

Specify the symbol shape to use. Allowed shapes are `circle`, `square`, `cross`, `diamond`, `triangle-up`, and `triangle-down`.

### path

#### path

Specify a path definition in the form of an SVG Path string.

### arc

#### inner_radius

The inner radius of the arc in pixels.

#### outer_radius

The outer radius of the arc in pixels.

#### start_angle

The start angle of the arc in radians.

#### end_angle

The end angle of the arc in radians.

### area

#### interpolate

Specify the line interpolation method to use. One of `linear`, `step-before`, `step-after`, `basis`, `basis-open`, `cardinal`, `cardinal-open`, and `monotone`.

#### tension

Specify the tension parameter depending on the interpolation type.

### line

#### interpolate

Specify the line interpolation method to use. One of `linear`, `step-before`, `step-after`, `basis`, `basis-open`, `basis-closed`, `bundle`, `cardinal`, `cardinal-open`, `cardinal-closed`, and `monotone`.

#### tension

Specify the tension parameter depending on the interpolation type.

### image

#### url

Specify the URL from which to retrieve the image.

#### align

Specify the horizontal alignment of the image. One of `left`, `right`, and `center`.

#### baseline

Specify the horizontal alignment of the image. One of `top`, `middle`, and `bottom`.

### text

#### text

Specify the text to display.

#### align

Specify the horizontal alignment of the image. One of `left`, `right`, and `center`.

#### baseline

Specify the horizontal alignment of the image. One of `top`, `middle`, and `bottom`.

#### dx

Specify the horizontal margin between the text label and its anchor point.

#### dy

Specify the vertical margin between the text label and its anchor point.

#### angle

Specify the rotation angle of the text in degrees.

#### font

Specify the typeface to set the text in.

#### font_size

Specify the font size in pixels.

#### font_weight

Specify the font weight such as `bold`.

#### font_style

Specify the font style such as `italic`.

### group

Group marks have the same visual properties as `rect` marks. They can also contain children marks, as well as scales, axes and legends. 
