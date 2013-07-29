---
layout: post
title: "The Power of Ruby Metaprogramming"
category: gsoc2013
---

I am working on the Data Transform component of Plotrb, which is to generate compatible specification for the corresponding part in Vega. To quote from Vega's [wiki](https://github.com/trifacta/vega/wiki/Data-Transforms),

> A data transform performs operations on a data set prior to visualization. Common examples include filtering and grouping (e.g., group data points with the same stock ticker for plotting as separate lines). Other examples include layout functions (e.g., stream graphs, treemaps, graph layout) that are run prior to mark encoding.

Every Transform definition must have a valid "type" to specify the transform to apply. Different types of transform have different properties, of which some are optional while others are mandatory.

For example, `filter` transform has only one property called `test`, which is an expression for the filter predicate.

```json
{"type": "filter", "test": "d.data.x > 10"}
```

Whilst `zip` transform, which merges two data sets together according to the provided join key, has five properties, namely `with`, `as`, `key`, `withKey`, `default`.

```json
{
  "type": "zip",
  "key": "data.id",
  "with": "unemployment",
  "withKey": "data.key",
  "as": "value"
}
```

_(For the purpose of this article, there is no need to dive into the details of each transform.)_

When I was designing the Transform class for Plotrb, I had several goals in mind:

1. Each _type_ of Transform should have different properties according to the Vega specification.
2. The properties should be easily accessible, preferably in the form of attr_accessors.
3. Each _instance_ of Transform should keep track of what properties are defined, and they may vary even for the same _type_ of Transforms due to optional properties.

So here's the solution I came up with. It definitely looks beautiful to me, thanks to the metaprogramming power of Ruby. Of course, I welcome any other more elegant approaches.

First of all, using inheritance to create separate sub-classes for each type of Transform is absolutely a no-go for me. I abhor even thinking about this idea.

When instantiating a Transform, two arguments are passed in. They are the `type` and the `args` as a Hash. After passing the validation of `type` and corresponding `args`, the following method is used to set the properties for that particular instance. It uses `self.singleton_class` so that the attr_accessors will not pollute other instances.

```ruby
def set_properties(args)
  args.each do |k, v|
    self.singleton_class.class_eval do
      attr_accessor k
    end
    self.instance_variable_set("@#{k}", v)
  end
end
```

However, this still does not solve the last problem. Since Ruby's `attr_accessor` only defines a setter and getter method for the instance variable, there is no direct way to know what instance variables are defined via `attr_accessor`.

In order to add the ability to keep track of the `attr_accessor` properties, a simple workaround would be to override the `attr_accessor` method, and store the properties in another instance variable.

```ruby
def self.attr_accessor(*vars)
  @properties ||= []
  @properties.concat(vars)
  super(*vars)
end

def properties
  self.singleton_class.instance_variable_get(:@properties)
end
```

And that's it. With fewer than 20 lines of Ruby code, we've achieved all three goals which would require much more effort to do in most of the other languages.

The full transform.rb is [here](https://github.com/zuhao/plotrb/blob/master/lib/plotrb/transforms.rb), but it's still work-in-progress.