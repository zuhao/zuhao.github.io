---
layout: post
title: "Plotrb Exampe: Arc"
category: gsoc2013
---

This is an example of drawing a interactive Arc chart with Plotrb.

```ruby
require 'plotrb'

data = pdata.name('table').values([12,23,47,6,52,19]).transform(pie_transform)

scale = sqrt_scale.name('r').from(data).to([20,100])

mark = arc_mark.from(data) do
	enter do
		x_start { group(:width).times(0.5) }
		y_start { group(:height).times(0.5) }
		start_angle { from :start_angle }
		end_angle { from :end_angle }
		inner_radius 20
		outer_radius { scale(scale) }
		stroke '#fff'
	end
	update do
		fill '#ccc'
	end
	hover do
		fill 'pink'
	end
end

vis = visualization.name('arc').width(400).height(400) do
	data data
	scales scale
	marks mark
end

puts vis.generate_spec(:pretty)
```

Take a look at this [js fiddle](http://jsfiddle.net/zuhao/c6Kuk/) to see how the spec is rendered in the browser.