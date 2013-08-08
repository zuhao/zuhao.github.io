---
layout: post
title: "How to Get Data into Plotrb"
category: gsoc2013
---

Acknowledgment: This tutorial is based on Vega's [documentation](https://github.com/trifacta/vega/wiki/Data).
___

The very essence of a plot is the data behind it, and thus the information it is trying to convey. In this tutorial, I'm going to talk about how to get the data into Plotrb.

A Data class does not have many attributes (and it's not supposed to). The goal is to let you import the data as effortlessly as possible.

### name

The name of the data set must be _unique_, so that you may reference it at other places of Plotrb.

### source

Use an existing data set as source of the new data set.

You can pass in the name of the other data.

```ruby
data.source('some_other_data')
```

You can also pass in other data object directly.

```ruby
some_data = Plotrb::Data.new.name('some_data')
new_data = Plotrb::Data.new.name('new_data').source(some_data)
```

### url

The actual data can come from a url, for example a json file from some web service.

```ruby
data.url('http://some/web/service/data.json')
```

### file

Similar to `url`, the data may also come from a local file, for example a csv file in another directory.

```ruby
data.file('./some/other/dir/data.csv')
```

### values

The data do not necessarily have to come from external sources. Instead of referencing the data source, you can also pass in the actual data values directly to Plotrb.

Currently, Plotrb supports several value types. You can pass in the values as an array.

```ruby
data.values([1,2,3,4,5])
```

You can also pass in a hash.

```ruby
data.values(foo: 1, bar: 2, baz: 3)
```

Or you can pass in JSON as string directly.

```ruby
data.values('{"foo":1, "bar":{"baz":2}}')
```

### transform

Specify the transforms to be performed on the data.

### format

As of now, Plotrb supports five types of data formats, namely `csv`, `tsv`, `json`, `topojson`, and `treejson`.

To assign specific attribtues for a format, you have to define the format first.

```ruby
data.format(:csv)
```

You can also use the following grammar.

```ruby
data.as_csv
```

(Manipulating attributes for a format must be done inside a block, as you will see in the following examples.)

* csv

	##### parse

	`parse` defines the data type for each field of the data set. Valid data types are `number`, `boolean`, and `date`.

	```ruby
	data.as_csv do
	  parse 'foo' => :number, 'bar' => :date
	end
	```

	Meanwhile, you can always do the following,

	```ruby
	data.as_csv do
	  as_number 'foo'
	  as_date 'bar'
	end
	```

	Or,

	```ruby
	data.as_csv do
	  number 'foo', 'bar'
	  date 'baz'
	end
	```

* tsv

	See `csv`.

* json

	##### parse

	See `csv`.

	##### property

	Set the JSON property containing the desired data.

* topojson

	##### feature

	Specify the name of the TopoJSON object to convert to a GeoJSON feature collection.

	##### mesh

	Specify the name of the TopoJSON object to convert to a mesh.

* treejson

	##### parse

	See `csv`.

	##### children

	Set the JSON property that contains an array of children nodes for each intermediate node.